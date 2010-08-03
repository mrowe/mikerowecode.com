First adventures in Clojure

I've been banging on to anyone who'd listen for ages now about how
[Clojure][] is going the be the Next Big Thing. I read a fair way
into Stuart Halloway's [Programming Clojure][], and I played in the
REPL a bit here and there, but I never got around to doing anything
serious with it.

[Clojure]: http://clojure.org/
[Programming Clojure]: http://pragprog.com/titles/shcloj/programming-clojure

Today I finally found an excuse to use Clojure at work for a
real-world problem. I needed to write a small program to read a
product feed in CSV format, and cross-check that all the products in
the feed actually exist in the live product catalogue database.

Here is my somewhat naïve attempt at implementing a solution:

    ;;
    ;; Read a CSV file and look up the product ids it contains in a
    ;; database. Report all the products in the CSV that do not exist in
    ;; the database.
    ;;
    ;; Usage: $0 <path-to-csv-file>
    ;;
    
    (import 'java.io.FileReader 'au.com.bytecode.opencsv.CSVReader)
    
    (use 'clojure.contrib.str-utils)
    (use 'clojure.contrib.sql)
    
    ;; OpenCSV gives us a List of String[]s... ugh.
    (defn read-csv [file-name]
      (with-open [reader (CSVReader. (FileReader. file-name))]
         (rest ;; skip the header row
          (map seq (seq (. reader readAll))))))
    
    ;; extract interesting fields from a CSV row
    (defn product-from [row]
      {:product-id (nth row  0 "")
       :title      (nth row  1 "")})
    
    ;; set up the db connection
    (def db {:classname   "org.h2.Driver"
             :subprotocol "h2"
             :subname (str "file:///Users/mrowe/.h2data/mydata")
             :user     "sa"
             :password ""})
    
    (defn sql-query [q]
      (with-query-results res q (doall res)))
    
    (defn count-products [product-id]
      (:count
       (first
        (sql-query ["select count(1) as count from product where id = ?" product-id]))))
    
    (defn exists? [product-id]
       (>= (count-products product-id) 1))
    
    (defn product-missing? [csv-row]
      (let [product (product-from csv-row)]
        (not (exists? (product :product-id)))))
    
    ;;;;;;;;;;
    
    (def filename (first *command-line-args*))
    (def feed (read-csv filename))
    
    (defn report-product-id [row]
      (let [product (product-from row)]
        (format "Not in product catalog: %s - %s" (product :product-id) (product :title))))
    
    (with-connection db 
      (println (str-join "\n" (map report-product-id (filter product-missing? feed)))))

This was purely an exercise in thinking functionally, and figuring out
the basics of driving Clojure and getting it to interact with the
world around it. I've made no attempt to actually use one of Clojure's
headline features, concurrency. (For what it's worth, it happily
processes an input of 2500 rows in a few seconds, most of which is
spent in the database--I doubt there's much to be gained from
parallelising it.) But I think it reads pretty well, and is at least
as concise and expressive as the equivalent Ruby would have been--once
you learn to see through all the parentheses. ;-)

Let me know what you think!

_Update_: I've put the above code on github: [http://gist.github.com/505633](http://gist.github.com/505633)
