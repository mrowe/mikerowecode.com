---
layout: post
title: Multiple instances of the iiNet Usage Widget
date: "2007-11-08"
url: "/2007/11/iinet_usage_widget_patch.html"
---

There is a very handy [usage widget][1] for iiNet available at
[LemonJar][2]. However, the way it stores its preferences for iiNet
account and password means that you can't run multiple instances of
the widget to monitor multiple iiNet accounts.

This patch fixes it so you can:

    --- MAIN.js.ORIG    2007-11-08 11:35:07.000000000 +1100
    +++ MAIN.js 2007-11-08 11:33:59.000000000 +1100
    @@ -144,14 +144,18 @@
     
     }
     
    +function keyForUsername() { return widget.identifier + "-" + "userName"; }
    +function keyForPassword() { return widget.identifier + "-" + "psword"; }
    +function keyForAlertCol() { return widget.identifier + "-" + "alertColorOn"; }
    +
     //Read in Username & Password Stored in OS .plist. Updates Global Variables.
     function readPrefs(){
        debug("Function: readPrefs() run.");
     
        if(window.widget) { 
    -       var TMPuserName = widget.preferenceForKey("userName"); 
    -       var TMPpsword = widget.preferenceForKey("psword"); 
    -       var TMPalertColorOn = widget.preferenceForKey("alertColorOn"); 
    +        var TMPuserName = widget.preferenceForKey(keyForUsername()); 
    +       var TMPpsword = widget.preferenceForKey(keyForPassword()); 
    +       var TMPalertColorOn = widget.preferenceForKey(keyForAlertCol()); 
     
            if ( TMPuserName && TMPuserName.length > 0) { 
                userName = TMPuserName;
    @@ -199,10 +203,10 @@
        alertColorOn = document.getElementById("alertColorPref").checked;
     
        if(window.widget){  
    -       widget.setPreferenceForKey(document.getElementById("userNamePref").value, "userName");
    -       widget.setPreferenceForKey(rot13(document.getElementById("pswordPref").value), "psword");
    -       widget.setPreferenceForKey(document.getElementById("alertColorPref").checked, "alertColorOn");
    -   }   
    +      widget.setPreferenceForKey(document.getElementById("userNamePref").value, keyForUsername());
    +      widget.setPreferenceForKey(rot13(document.getElementById("pswordPref").value), keyForPassword());
    +      widget.setPreferenceForKey(document.getElementById("alertColorPref").checked, keyForAlertCol());
    +   }
     }

LemonJar also make widgets for other Australian ISPs. I suspect this
patch would also work for those widgets (on the assumption that the
code in MAIN.js is common), but I haven't tested it.

For what it's worth, I filed a [bug][3] in their issue tracker.

[1]: http://lemonjar.com/?p=products.widgets.iinet
[2]: http://lemonjar.com
[3]: http://bugs.lemonjar.com/1.1.0rc2/view.php?id=2 
