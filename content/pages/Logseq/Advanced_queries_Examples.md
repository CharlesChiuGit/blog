---
title: Logseq/Advanced_queries_Examples
public: true
tags:
categories:
date: 2022-04-08
lastMod: 2022-04-21
---
This page is a collection of some of the Advanced queries from the Datalog channel on the Logseq/Discord server.
### Resources

  + [link: The first message on the datalog channel](https://discord.com/channels/725182569297215569/743139225746145311/743139795865174119)

  + [link: Logseq docs - Advanced queries](https://docs.logseq.com/#/page/advanced%20queries)

  + [link: Logseq datascript schema](https://gist.github.com/tiensonqin/9a40575827f8f63eec54432443ecb929)

  + [link: Logseq frontend db model](https://github.com/logseq/logseq/blob/master/src/main/frontend/db/model.cljs)

  + [link: How to Graph Your Data - talk by Paula Gearon](https://youtu.be/tbVwmFBnfo4)

  + [link: Domain modelling with datalog - talk by Norbert Wojtowicz](https://youtu.be/oo-7mN9WXTw)

  + [link: Athens Research ClojureFam](https://github.com/athensresearch/ClojureFam)

  + [link: Learn datalog today](http://www.learndatalogtoday.org)

  + [link: Uday Verma's Blog: Datascript 101](https://udayv.com/blog/2016-04-28-datascript101/)

  + [link: ClojureScript Cheatsheet](https://cljs.info/cheatsheet/)

  + [link: Hiccup syntax](https://github.com/weavejester/hiccup/wiki/Syntax)

  + [link: Logseq datascript](https://github.com/logseq/datascript/blob/fork/src/datascript/query.cljc)

  + [link: Advanced queries tutorial](https://mschmidtkorth.github.io/logseq-msk-docs/#/page/advanced%20queries%20tutorial)

### Queries

  + **Query to search a textual input**
link:: [Discord](https://discord.com/channels/725182569297215569/743139225746145311/821317203910721577)
date:: 2021-03-16


    + #+BEGIN_QUERY
{:title "Advanced query to search for the text 'Quantum mechanics'"
:query
 [:find (pull ?b [*])
  :in $ ?pattern
  :where
  [?b :block/content ?c]
  [(re-pattern ?pattern) ?q]
  [(re-find ?q ?c)]]
 :inputs ["Quantum mechanics"]
 :collapsed? true}
#+END_QUERY

    + Equivalent to `{{query "Quantum mechanics"}}`

  + **Find all TODO tasks outside of the journal pages**
link:: [Discord](https://discord.com/channels/725182569297215569/743139225746145311/824060683947737138)
date:: 2021-03-24


    + #+BEGIN_QUERY
{:title [:h2.font-bold.opacity-70 "TODO"]

    :query [
            :find (pull ?h [*])
            :where
                [?h :block/marker ?marker]
                [(= ?marker "TODO")]
            ]
    :result-transform (fn [result]
                        (sort-by (fn [h]
                                   (get h :block/priority "Z")) result))
    :collapsed? true}

#+END_QUERY

  + **All TODO blocks recursively related to a tag or page**
link:: [Discord](https://discord.com/channels/725182569297215569/743139225746145311/825330146780381205)
date:: 2021-03-27


    + How would you query for all TODO blocks that are tagged or recursively related to ancestor block/page that is tagged with a specific tag? For example, I want all TODO tasks that are tagged as clojure or contained under blocks or pages (in any indentation depth) that are tagged clojure. That way I can see all the clojure related articles I haven't read yet, without having to tag them all explicitly.
link:: [Discord](https://discord.com/channels/725182569297215569/743139225746145311/825074937651396678)

    + #+BEGIN_QUERY
{:title "All TODO items related to the tag 'Cowling' "
:query
 [:find (pull ?b [*])
  :where
  [?b :block/marker "TODO"]
  [?b :block/page ?p]
  (or [?b :block/path-ref-pages [:page/name "cowl"]]

      [?p :page/tags [:page/name "cowl"]])

]
 :collapsed? true}
#+END_QUERY

  + **Scheduled items and deadlines from the past week**
link:: [Discord](https://discord.com/channels/725182569297215569/743139225746145311/827098887826964490)
date:: 2021-04-01


    + Hey, I try to find Schedules or Due dates that are in-between specific dates, but whenever they contain a marker (TODO, NOW, ...) they should only show if that marker is not completed. Unfortunately, I'm not familiar with Datomic or Clojure at all..
link:: [Discord](https://discord.com/channels/725182569297215569/743139225746145311/826412758438248458)

    + #+BEGIN_QUERY
{:title "?�� SLIPPING deadlines (<1W)"

    :query [:find (pull ?block [*])
            :in $ ?start ?today
            :where
            (or
              [?block :block/scheduled ?d]
              [?block :block/deadline ?d])
            (not
              [?block :block/marker ?marker] 
              (not [(contains? #{"NOW" "LATER" "TODO" "DOING"} ?marker)]))
            [(>= ?d ?start)]
            [(<= ?d ?today)]]
    :inputs [:8d :today]
    :collapsed? true}

#+END_QUERY

  + **Find all unlinked pages**
link:: [Discord](https://discord.com/channels/725182569297215569/743139225746145311/832512082289229824)
date:: 2021-04-16


    + query-table:: false
#+BEGIN_QUERY
{:title "Orphan pages"
 :query [:find ?name

         :where
         [?p :page/name ?name]
         (not
          [?b :block/ref-pages ?p1]
          [?b :block/page ?p2]
          (or [?p1 :page/name ?name]
              [?p2 :page/name ?name]))]

 :view (fn [result]

         [:div.flex.flex-col
          (for [page result]
            [:a {:href (str "/page/" page)} (clojure.string/capitalize page)])])

:collapsed? true}
#+END_QUERY

  + **List of currently DOING tasks**
link:: [Discord](https://discord.com/channels/725182569297215569/743139225746145311/848575612385624074)
date:: 2021-05-30



    + query-table:: false

#+BEGIN_QUERY
{:title "?�� DOING"

    :query [:find (pull ?b [*])
        :where
          [?b :block/marker ?marker]
          [(contains? #{"NOW" "DOING"} ?marker)]]

:breadcrumb-show? false
 :result-transform (fn [result]

         (sort-by (fn [h]
         (get h :block/priority "Z"))
       result))
    :collapsed? true}

#+END_QUERY

  + **Query to show random block**
link:: [Discord](https://discord.com/channels/725182569297215569/743139225746145311/867362494871765002)
date:: 2021-07-21


    + #+BEGIN_QUERY
{:title "Give me a random block!!!"
 :query [:find (pull ?b [*])

         :where
         [?b :block/content ?content]
         (not [(empty? ?content)])]

 :result-transform (fn [result]

                     [(rand-nth result)])

 :collapsed? true}
#+END_QUERY

  + **Query to show a random page**
link:: [Discord](https://discord.com/channels/725182569297215569/743139225746145311/867375290396311633)
date:: 2021-07-21


    + #+BEGIN_QUERY
{:title "Give me a random page!!!"
 :query [:find ?name

         :where
         [?b :block/name ?name]]

 :result-transform (fn [result]

                     [(rand-nth result)])

:view (fn [result]

       [:div.flex.flex-col
        (for [page result]
          [:a {:href (str "/#/page/" page)} (clojure.string/capitalize page)])])

 :collapsed? true}
#+END_QUERY

  + **Search by block uuid**
link:: [Discord](https://discord.com/channels/725182569297215569/743139225746145311/868359519678398504)
date:: 2021-07-24


    + #+BEGIN_QUERY
{:query [:find (pull ?b [*]) :in $ ?uuid

      :where
      [?b :block/uuid ?u]
      [(str ?u) ?s]
      [(= ?uuid ?s)]]

 :inputs ["61d9ce9c-0822-4c1d-a9f3-78537657155b"]
}
#+END_QUERY

  + **Query backlog TODO and DOING tasks**
link:: [Discord](https://discord.com/channels/725182569297215569/743139225746145311/868476460061577227)
date:: 2021-07-24


    + #+BEGIN_QUERY 
{:title "Backlog"
:query [:find (pull ?todo [*]) :in $ ?current-page
:where
[?p :block/name ?current-page]
[?todo :block/marker ?marker]
(not [?todo :block/page ?p])
(not [_ :block/refs ?todo])
[(contains? #{"TODO" "DOING"} ?marker)]
]
:inputs [:current-page]
 :breadcrumb-show? false
 :result-transform (fn [result]

                        (sort-by (fn [h]
                                   (get h :block/priority "Z")) result))

 :collapsed? true}
#+END_QUERY

  + **Search for a keyword within a specific set of pages**
link:: [Discord](https://discord.com/channels/725182569297215569/743139225746145311/868531920923918356), [Discord](https://discord.com/channels/725182569297215569/743139225746145311/868600535694082049)
date:: 2021-07-24


    + #+BEGIN_QUERY
{:title "Siloed search"
 :query [:find (pull ?b [*])

         :in $ ?keyword [?title ...]
         :where
         [?p :block/original-name ?title]
         [?b :block/page ?p]
         [?b :block/content ?c]
         [(clojure.string/includes? ?c ?keyword)]]

 :inputs ["tasks" ["Advanced queries" "Advanced queries/Examples"]]
 }
#+END_QUERY

    + #+BEGIN_QUERY
{:title "Siloed search"
 :query [:find (pull ?b [*])

         :in $ ?keyword [?title ...]
         :where
         [(str "(?i)" ?keyword) ?matcher]
         [(re-pattern ?matcher) ?regex]
         [?p :block/original-name ?title]
         [?b :block/page ?p]
         [?b :block/content ?c]
         [(re-find ?regex ?c)]]

 :inputs ["tasks" ["Advanced queries" "Advanced queries/Examples"]]
 }
#+END_QUERY

  + **Broken references**
link:: [Discord](https://discord.com/channels/725182569297215569/743139225746145311/869291105911447653)
date:: 2021-07-26


    + #+BEGIN_QUERY 
{:title "Broken References"
 :query [:find (pull ?b [*])

         :in $ ?matcher
         :where
         [(re-pattern ?matcher) ?regex]
         [?b :block/content ?c]
         [(re-find ?regex ?c)]
         [(missing? $ ?b :block/refs)]]

 :inputs [ "\\([0-9a-f]{8}-[0-9a-f]{4}-[0-9a-f]{4}-[0-9a-f]{4}-[0-9a-f]{12}\\)"]
 }
#+END_QUERY

  + **Current NOW and DOING tasks for pages tagged with "project"**
link:: [Discord](https://discord.com/channels/725182569297215569/743139225746145311/879621160860532766)
date:: 2021-08-24


    + #+BEGIN_QUERY
{:title "?�� Project Now"

    :query [:find (pull ?h [*])
        :in $ ?searchterm
        :where
        [?h :block/marker ?marker]
        [(contains? #{"NOW" "DOING"} ?marker)]
        [?p :block/properties ?a]
        [(get ?a :type) ?t]
        [(= ?searchterm ?t)]
        [?br :block/name ?searchterm]
        (or [?h :block/page ?p]
            [?h :block/refs ?br])
        ]
    :inputs ["project"]
    :result-transform (fn [result]
        (sort-by (fn [h]
            (get h :block/priority "Z")) result))
    :collapsed? false}

#+END_QUERY

  + **Querying TODOs from hierarchy pages**
link:: [Discord](https://discord.com/channels/725182569297215569/743139225746145311/880431776500179004)
date:: 2021-08-06


    + #+BEGIN_QUERY
{:query [:find (pull ?b [*])

         :in $ ?s
         :where
         [?b :block/marker "DONE"]
         [?b :block/page ?p]
         [?p :block/original-name ?n]
         [(clojure.string/starts-with? ?n ?s)]]

 :inputs [ "Advanced queries/" ]
 }
#+END_QUERY

    + DONE Completed this query

  + **List all the pages updated in the last 3 days**

link:: [Discord](https://discord.com/channels/725182569297215569/743139225746145311/884486589827395724),  [Discord](https://discord.com/channels/725182569297215569/743139225746145311/885529176730398821)
date:: 2021-09-06, 2021-09-09

    + The time entered in the Unix time stamp (see https://www.unixtimestamp.com) which is the number of milliseconds since Jan 1st 1970.
query-properties:: [:page :updated-at]

    + We may have to multiply with 1000 to get the result in milliseconds, if using the above site.

    + query-sort-by:: updated-at
query-sort-desc:: true
query-properties:: [:page :updated-at]
#+BEGIN_QUERY
{:title [:h3 "Updated in last 3 days"]
 :query [:find (pull ?b [*])
:in $ ?end
:where
[?b :block/updated-at ?v]
[(- ?end 259200000 ) ?period]
[(>= ?v ?period)]
[(< ?v ?end)]
]
; :inputs [:start-of-today-ms]
; :inputs [:right-now-ms]
:inputs [:end-of-today-ms]
}
#+END_QUERY

  + **Query for TODO and LATER but excluding SCHEDULED tasks**
link:: [Discord](https://discord.com/channels/725182569297215569/743139225746145311/888689090160103476)
date:: 2021-09-18


    + #+BEGIN_QUERY
{:query [:find (pull ?b [*])
 :where
 [?b :block/marker ?m]
 [(contains? #{"TODO" "LATER"} ?m)]
 [(missing? $ ?b :block/scheduled)]
 [(missing? $ ?b :block/deadline)] ] }
#+END_QUERY

  + **Query for a task having a given block property**
link:: [Discord](https://discord.com/channels/725182569297215569/743139225746145311/889013656409505802)
date:: 2021-09-19


    + #+BEGIN_QUERY
{:title "My TASKS (type: example)"
:query [:find (pull ?b [*])
:where
[?b :block/marker ?marker]
[(contains? #{"DONE"} ?marker)]
[?b :block/properties ?prop]
[(get ?prop :type) ?type]
[(= "example" ?type)]
]
:collapsed? false}
#+END_QUERY

    + DONE Check query with task and property.
type:: example

  + **List all projects (type:: project) that do not have any TODOs on their pages**
link:: [Discord](https://discord.com/channels/725182569297215569/743139225746145311/891274363062599702)
date:: 2021-09-25


    + #+BEGIN_QUERY
{:query[:find (pull ?p [*]) 
  :where
  [?b :block/page ?p]
  [?p :block/properties ?props]
  [(get ?props :type) ?type]
  [(= ?type "project")]
  (not-join [?p]

    [?b :block/page ?p]
    [?b :block/marker _]) ]}

#+END_QUERY

  + **List all the projects with page property "status" as :Active**

link:: [Discord](https://discord.com/channels/725182569297215569/743139225746145311/891910125667905546)
date:: 2021-9-27

    + #+BEGIN_QUERY
{:query[:find (pull ?p [*]) 
  :where
  [?b :block/page ?p]
  [?p :block/properties ?props]
  [(get ?props :status) ?status]
  [(= ?status #{"Active"})]]}
#+END_QUERY

  + **Query all tasks excluding those on a page Journal reviews]]**

link:: [Discord](https://discord.com/channels/725182569297215569/743139225746145311/892166421029728256), [Discord](https://discord.com/channels/725182569297215569/743139225746145311/892189369622990949)
date:: 2021-09-27

    + #+BEGIN_QUERY
{:title "?�� TESTING"

      :query [:find (pull ?b [*])
              :where
              [?b :block/marker ?marker]
              [(missing? $ ?b :block/scheduled)]
              [(missing? $ ?b :block/deadline)]
              [(contains? #{"LATER" "TODO"} ?marker)]
              [?b :block/page ?page]
              [?page :block/original-name ?name]
              (not[(= ?name "Journal reviews")])]

 }
#+END_QUERY

    + #+BEGIN_QUERY
{:title "Main Tasks"

      :query [:find (pull ?b [*])
              :where
              [?b :block/marker ?marker]
              [(missing? $ ?b :block/scheduled)]
              [(missing? $ ?b :block/deadline)]
              [(contains? #{"LATER" "TODO"} ?marker)]
              (not [?b :block/path-refs [:block/name "test"]])
              [?b :block/page ?page]
              [?page :block/original-name ?name]
              (not[(= ?name "Journal reviews")])]

 :breadcrumb-show? false
 :result-transform (fn [result]

                        (sort-by (fn [h]
                                   (get h :block/created-at)) result))

:collapsed? true
 }
#+END_QUERY

  + DONE **Query all scheduled/deadlined tasks and sort them by scheduled or deadline date**
link:: [Discord](https://discord.com/channels/725182569297215569/743139225746145311/897615821688410114)
date:: 2021-10-13

SCHEDULED: <2022-01-30 Sun>

    + #+BEGIN_QUERY
{:title "?�� Future Tasks"
 :query [:find (pull ?block [*])

         :in $ ?start ?next
         :where
         [?block :block/marker ?marker]
         [(contains? #{"NOW" "LATER" "TODO" "DOING" "WAITING"} ?marker)]
         (or
           [?block :block/scheduled ?d]
           [?block :block/deadline ?d])
         [(>= ?d ?start)]
         [(<= ?d ?next)]]

 :inputs [:today :365d-after]
 :breadcrumb-show? false
 :result-transform (fn [result]

                     (sort-by (fn [b]
                               (or (get b :block/scheduled) (get b :block/deadline))) result))

 :collapsed? true
}
#+END_QUERY

  + **All tasks with priority A**
link:: [Discord](https://discord.com/channels/725182569297215569/743139225746145311/898234502890004532)
date:: 2021-10-14


    + #+BEGIN_QUERY
{:title "All todos that with priority A"
 :query [:find (pull ?b [*])

       :where
       [?b :block/priority "A"]]}

#+END_QUERY

  + **Query snippet to grab the children blocks**
link:: [Discord](https://discord.com/channels/725182569297215569/743139225746145311/901520859779461170)
date:: 2021-10-23


    + Replace `(pull ?h [*])` with:

```clojure
#+BEGIN_QUERY
(pull ?h [
    :db/id
    :block/uuid
    :block/type
    :block/left
    :block/format
    :block/title
    :block/refs
    :block/_refs
    :block/path-refs
    :block/tags
    :block/content
    :block/marker
    :block/priority
    :block/properties
    :block/body
    :block/pre-block?
    :block/scheduled
    :block/deadline
    :block/repeated?
    :block/created-at
    :block/updated-at
    :block/file
    :block/parent
    :block/unordered
    :block/heading-level
    {:block/page
    [:db/id :block/name :block/original-name :block/journal-day]}
    {:block/_parent ...}])
```

  + **Query for non-task blocks with a schedule/deadline in the past**

link:: [Discord](https://discord.com/channels/725182569297215569/743139225746145311/905889607705575434)
date:: 2021-11-04

    + query-table:: false
#+BEGIN_QUERY 
{:title "Old Blocks"
 :query         [:find (pull ?b [*])

                 :in $ ?start
                 :where
                 (or [?b :block/scheduled ?d]
                     [?b :block/deadline ?d])
                 [(< ?d ?start)]
                 (not [?b :block/marker ?marker] 
                  [(contains? #{"DONE" "CANCELED"} ?marker)])]

 :inputs [:today]
 :collapsed? false}
#+END_QUERY

  + **Query to find tasks under a block containing the text "Follow-up"**
link:: [Discord](https://discord.com/channels/725182569297215569/743139225746145311/918847233586253906)
date:: 2021-12-10


    + Follow-up

      + DONE find this block

      + #+BEGIN_QUERY
  {:title "Find: Follow-up Tasks"
  :query [:find (pull ?b [*])
  :where

     [?p :block/content "Follow-up"]
     [?b :block/parent ?p]
     [?b :block/marker ?marker]
     [(contains? #{"TODO" "DOING" "DONE"} ?marker)]]

}
  #+END_QUERY

  + **Query all pages that have been created in the last 2 days**
link:: [Discord](https://discord.com/channels/725182569297215569/743139225746145311/920559641514082374)
date:: 2021-12-15


    + query-properties:: [:page :created-at]
#+BEGIN_QUERY
{:title "Pages in last 2 days"
:query [:find (pull ?p [*])

            :in $ ?today
            :where 
            [?p :block/created-at ?d]
            [(- ?today 172800000) ?start]
            [(>= ?d ?start)]
            [(<= ?d ?today)]
    ]

:inputs [:right-now-ms]}
#+END_QUERY

    + `172800000` is 2 days in ms.

  + **Query to create a table with page and todo count**
link:: [Discord](https://discord.com/channels/725182569297215569/743139225746145311/921337299164356658)
date:: 2021-12-17


    + query-table:: false
#+BEGIN_QUERY 
{:title "TODO by page"
  :query     [:find (pull ?b [:block/marker :block/parent {:block/page

     [:db/id :block/name]}])

  :where

           [?b :block/marker ?marker]
           [(= "TODO" ?marker)] 

  ]
:result-transform (fn [result]

                        (map (fn key value {:page (get key :block/name) :count (count value)}) (group-by :block/page result))
                )

:view (fn [rows] [:table 
 [:thead 
  [:tr 
   [:th "Page"] 
   [:th "Count"] ] ] 
 [:tbody 
(for [r rows] [:tr 
   [:td [:a {:href (str "#/page/" (get r :page))} (get r :page)] ] 
   [:td (get r :count)] ])
   ]]
)
}
#+END_QUERY

  + **Simple query to finds all the tasks created today**
link:: [Discord](https://discord.com/channels/725182569297215569/743139225746145311/923276145321967676), [Discord](https://discord.com/channels/725182569297215569/743139225746145311/923282510283169812), [Discord](https://discord.com/channels/725182569297215569/743139225746145311/923486975552528424)
date:: 2021-12-22


    + {{query (and (between -0d ++0d) (task TODO))}}

    + d is used to indicate a day. 0d means 0 days or today. 
Think of it this way:
Between  -0d and ++0d

      + = between today-1 and today+1
= between yesterday and tommrow 
= today

    + ++1d transformed to todays date
+1d transformed to tomorrow
-1d transformed to yesterday

  + **Query all blocks on the current page having a specific tag, say "#datalog"**
link:: [Discord](https://discord.com/channels/725182569297215569/743139225746145311/923925207738109962)
date:: 2021-12-24


    + #+BEGIN_QUERY
{

    :query [
        :find (pull ?b [*]) 
        :in $ ?current-page
        :where 
            [?p :block/name ?current-page]
            [?b :block/page ?p]
            [?b :block/path-refs [:block/name "datalog"]]
    ]
    :inputs [:current-page]}

}
#+END_QUERY

  + **Query to create a table from "birthday" property of a page**
    + query-table:: false
query-properties:: [:alias :birthday]
#+BEGIN_QUERY
{
 :query [:find (pull ?b [*])

      :where
      [?b :block/properties ?bprops]
      [(get ?bprops :birthday "nil") ?bs]
      [(not= ?bs "nil")]]

:result-transform (fn [result]

    (sort-by (fn [h]
      (get-in h [:block/properties :verjaardag])) result))

}
#+END_QUERY
