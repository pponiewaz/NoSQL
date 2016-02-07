### Aggregation Pipeline  
Zaimportowana baza reddita - [RC_2015_01](https://www.reddit.com/r/datasets/comments/3bxlg7/i_have_every_publicly_available_reddit_comment).  

__Do wykonania zadania wykorzystalem:__  
Python: 2.7.3  
PyMongo: 3.2
#### 1. Wyświetlenie przykładowego rekordu.
```javascript
db.reddit.findOne()
```
Wynik:  
```javascript
{
        "_id" : ObjectId("5650c2c1a531954c71ad9370"),
        "score_hidden" : false,
        "name" : "t1_cnas8zv",
        "link_id" : "t3_2qyr1a",
        "body" : "Most of us have some family members like this. *Most* of my family is like this. ",
        "downs" : 0,
        "created_utc" : "1420070400",
        "score" : 14,
        "author" : "YoungModern",
        "distinguished" : null,
        "id" : "cnas8zv",
        "archived" : false,
        "parent_id" : "t3_2qyr1a",
        "subreddit" : "exmormon",
        "author_flair_css_class" : null,
        "author_flair_text" : null,
        "gilded" : 0,
        "retrieved_on" : 1425124282,
        "ups" : 14,
        "controversiality" : 0,
        "subreddit_id" : "t5_2r0gj",
        "edited" : false
}
```

#### 2. Zapytania do bazy danych
__2a. Zliczanie kontrowerowersyjnych tematow.__
 JavaScript:
```javascript
db.reddit.aggregate([ {$group:{_id:"$author", controversiality: {$sum: "$controversiality" } } },
                    { $sort: {controversiality:-1}}, {$limit: 4} ],
                    {allowDiskUse:true}
                  )
```
Python:
```python
import pymongo
client = pymongo.MongoClient("localhost", 27017)
db = client.reddit

db.reddit.aggregate([ {"$group":{"_id":"$author", "controversiality": {"$sum": "$controversiality" } } },
                    { "$sort": {"controversiality":-1}}, {"$limit": 3} ]
                  )
```
__2b. Wyszukanie 5 najlepszych wpisow__  
JavaScript:
```javascript
db.reddit.aggregate([ {$group:{ _id: "$author", score: {$sum: "$score" } } },
                     { $sort: {score: -1} },
                     { $limit: 3} ],
                     { allowDiskUse: true}
                   )
```
Python:
```python
import pymongo
client = pymongo.MongoClient("localhost", 27017)
db = client.reddit

db.reddit.aggregate([ {"$group":{ "_id": "$author", "score": {"$sum": "$score" } } },
                     { "$sort": {"score": -1} }, { "$limit": 5} ],
                                        )
```
Wynik:
```javascript
{ "_id" : "[deleted]", "score" : 8855952 }
{ "_id" : "AutoModerator", "score" : 232750 }
{ "_id" : "PainMatrix", "score" : 187374 }
{ "_id" : "maisiewilliamsAMA", "score" : 92460 }
{ "_id" : "Smeeee", "score" : 91885 }
```
---
__2c. Zliczenie postow z ukrytym wynikiem__  
JavaScript:  
```javascript
db.reddit.aggregate([ {$match: { score_hidden: true } },
                    { $group: { _id: null, count: {$sum: 1} } } ],
                    {allowDiskUse: true}
                  )
```
Python:
```python
import pymongo
client = pymongo.MongoClient("localhost", 27017)
db = client.reddit

db.reddit.aggregate([ {"$match": { "score_hidden": "true" } },
                    { "$group": { "_id": null, "count": {"$sum": 1} } } ],
                  )
```
Wynik:
```javascript
{ "_id" : null, "count" : 102718 }
```
---
__2d. Wyswietlenie 10 najpopularniejszych redditow__  
JavaScript:
```javascript
db.reddit.aggregate([ {$group:{_id: "$subreddit", count: {$sum: 1} } },
                        { $sort : {count : -1} },
                        {$limit : 10} ],
                        { allowDiskUse: true}
                      )
```
Python:
```python
db.reddit.aggregate([ {"$group":{"_id" : "$subreddit", "count" : {"$sum" : 1} } },
                    { "$sort" : {"count" : -1} },
                    {"$limit" : 10}
                    ])
```
Wynik:
```javascript
{ "_id" : "AskReddit", "count" : 4712795 }
{ "_id" : "nfl", "count" : 932460 }
{ "_id" : "funny", "count" : 930098 }
{ "_id" : "leagueoflegends", "count" : 904297 }
{ "_id" : "pics", "count" : 778942 }
{ "_id" : "worldnews", "count" : 670872 }
{ "_id" : "todayilearned", "count" : 599295 }
{ "_id" : "DestinyTheGame", "count" : 587774 }
{ "_id" : "AdviceAnimals", "count" : 577463 }
{ "_id" : "videos", "count" : 570938 }
```
