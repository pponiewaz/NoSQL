### Aggregation Pipeline

#### 1. Wyszukanie pierwszych 10 kontrowersyjnych wpisow.

`db.reddit.aggregate([{$group: {_id: "$author", controversiality: {$sum: "$controversiality"}}},
                    {$sort: {controversiality: -1}}, {$limit: 10} ])`
