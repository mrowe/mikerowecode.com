How not to comment your code

Below is a piece of code I came across recently while reviewing
somebody else's work. It is a terrific example of how <b>not</b> to
write comments.

    //Create a vector
    Vector seq = new Vector();
    
    //Count the number of items in the sequence
    int count = ms.itemCount();
    
    //Loop through the sequence getting all the values
    for( int i = 0; i &lt; count; i++ ) {
        //Convert the item to an Instance
        MInstance mi = (MInstance)ms.getAt( i );
    
        //Convert the instance to a HashMap
        Map data = mInstanceToMap( mi );
    
        //Add this to the Vector
        seq.addElement( data );
    }
    //Return the vector
    return seq;

Ignoring for the minute the infuriating habit of putting spaces around
parameters (doesn't anybody read the Java style guide any more?),
these comments are completely useless.

There are plenty of places to learn how to write comments. A couple of
references come to mind: Steve McConnell's "Code Complete", "The
Pragmatic Programmer", "Programming Pearls".

In a nutshell... don't write comments that say *what* the code is
doing (you can safely assume that anyone likely to need to read your
comments is at least borderline literate in the progamming language in
question). Maybe explain *why* your code is doing it, and
defintely document any assumptions. Please don't tell me 
`new Vector()` creates a new Vector...
