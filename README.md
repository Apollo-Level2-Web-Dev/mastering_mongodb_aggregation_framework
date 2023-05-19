```
db.students.aggregate([
    { $match: { "scores.type": "exam" } }
]);
```

```
db.students.aggregate([
    { $group: { _id: "$scores.type" } }
]);

```

```
db.students.aggregate([
    {$match: {grade:"F+"}},
    {$count: "FaiL_Korse"}
]);

```

```
db.students.aggregate([
    {$sort: {name:1}}
]);

```

```
db.students.aggregate([
    { $match: { "scores.type": "exam" } },
    {$sort: {
        "scores.0.score":-1
    }}
]);

```

```
db.students.aggregate([
    { $match: { "scores.type": "exam" } },
    { $sort: { "scores.0.score": -1 } },
    {
        $project: {
            scores: {
                $slice: ["$scores", 1]
            }
        }
    }

]);

```

```
db.students.aggregate([
    { $match: { "scores.type": "exam" } },
    { $sort: { "scores.0.score": -1 } },
    {
        $project: {
            scores: {
                $slice: ["$scores", 1]
            }
        }
    },

    { $sort: { "scores.score": -1 } }

]);

```

```
db.students.aggregate([
    { $match: { "scores.type": "exam" } },
    // { $sort: { "scores.0.score": -1 } },
    // {
    //     $project: {
    //         scores: {
    //             $slice: ["$scores", 1]
    //         }
    //     }
    // },

    // { $sort: { "scores.score": -1 } },
    {$unwind: "$scores"}

]);
```

```
db.students.aggregate([
    { $unwind: "$scores" },
    { $match: { "scores.type": "exam" } }
]);

```

```
db.students.aggregate([
    { $match: { "scores.type": "exam" } },
    { $sort: { "scores.0.score": -1 } },
    {
        $project: {
            scores: {
                $slice: ["$scores", 1]
            }
        }
    },
    {$limit: 100}

]);
```

```
db.students.aggregate([
    { $unwind: "$scores" },
    { $match: { "scores.type": "exam" } },
    {$addFields: {
        "examFinalScore":{
            $round: ["$scores.score",2]
        }
    }},
    {$project: {
        name:1,examFinalScore:1
    }}
]);

```

```
db.students.aggregate([
    { $unwind: "$scores" },
    { $match: { "scores.type": "exam" } },
    {
        $addFields: {
            "examFinalScore": {
                $round: ["$scores.score", 2]
            }
        }
    },
    {
        $project: {
            name: 1, examFinalScore: 1
        }
    },
    {
        $addFields: {
            "grade": {
                $switch: {
                    branches: [
                        {
                            case: { $lt: ["$examFinalScore", 40] },
                            then: "F"
                        },
                        {
                            case: { $lt: ["$examFinalScore", 50] },
                            then: "D"
                        },
                        {
                            case: { $lt: ["$examFinalScore", 60] },
                            then: "C"
                        },
                        {
                            case: { $lt: ["$examFinalScore", 70] },
                            then: "B"
                        },
                        {
                            case: { $lt: ["$examFinalScore", 90] },
                            then: "A"
                        },
                        {
                            case: { $gte: ["$examFinalScore", 90] },
                            then: "A+"
                        }
                    ],
                    default: ""
                }
            }
        }
    },
    { $sort: { examFinalScore: -1 } },
    {$match: {grade:"A+"}},
    {$out: "A_plus_pawa_polapans"}
]);


```

```
db.students.aggregate([
    { $unwind: "$scores" },
    { $match: { "scores.type": "exam" } },
    {
        $addFields: {
            "examFinalScore": {
                $round: ["$scores.score", 2]
            }
        }
    },
    {
        $project: {
            name: 1, examFinalScore: 1
        }
    },
    {
        $addFields: {
            "grade": {
                $switch: {
                    branches: [
                        {
                            case: { $lt: ["$examFinalScore", 40] },
                            then: "F"
                        },
                        {
                            case: { $lt: ["$examFinalScore", 50] },
                            then: "D"
                        },
                        {
                            case: { $lt: ["$examFinalScore", 60] },
                            then: "C"
                        },
                        {
                            case: { $lt: ["$examFinalScore", 70] },
                            then: "B"
                        },
                        {
                            case: { $lt: ["$examFinalScore", 90] },
                            then: "A"
                        },
                        {
                            case: { $gte: ["$examFinalScore", 90] },
                            then: "A+"
                        }
                    ],
                    default: ""
                }
            }
        }
    },
    { $sort: { examFinalScore: -1 } },
    { $match: { grade: "A+" } },
    {
        $group: {
            _id: null,
            average: { $avg: {$round:"$examFinalScore"} }
        }
    }
]);

```

```
db.students.aggregate([
    { $unwind: "$scores" },
    { $match: { "scores.type": "exam" } },
    {
        $addFields: {
            "examFinalScore": {
                $round: ["$scores.score", 2]
            }
        }
    },
    {
        $project: {
            name: 1, examFinalScore: 1
        }
    },
    {
        $addFields: {
            "grade": {
                $switch: {
                    branches: [
                        {
                            case: { $lt: ["$examFinalScore", 40] },
                            then: "F"
                        },
                        {
                            case: { $lt: ["$examFinalScore", 50] },
                            then: "D"
                        },
                        {
                            case: { $lt: ["$examFinalScore", 60] },
                            then: "C"
                        },
                        {
                            case: { $lt: ["$examFinalScore", 70] },
                            then: "B"
                        },
                        {
                            case: { $lt: ["$examFinalScore", 90] },
                            then: "A"
                        },
                        {
                            case: { $gte: ["$examFinalScore", 90] },
                            then: "A+"
                        }
                    ],
                    default: ""
                }
            }
        }
    },
    { $sort: { examFinalScore: -1 } },
    {
        $setWindowFields: {
            sortBy: { examFinalScore: -1 },
            output: {
                position: { $rank: {} }
            }
        }
    }, {
        $setWindowFields: {
            sortBy: { examFinalScore: -1 },
            output: {
                position: { $rank: {} }
            }
        }
    },
], {
    allowDiskUse: true
});

```

```
db.students.aggregate([
    { $match: { "scores.type": "exam" } },
    { $project: { "scores": 1 } },
    {
        $addFields: {
            "scores": {
                $filter: {
                    input: "$scores",
                    as: "score",
                    cond: { $eq: ["$$score.type", "exam"] }
                }
            }
        }
    },
    { $unwind: "$scores" },
    { $sort: { "scores.score": -1 } },
    {
        $group: {
            _id: "$_id",
            scores: {
                $push: {
                    type: "$scores.type",
                    score: { $round: ["$scores.score", 2] }
                }
            }
        }
    },
    { $project: { "_id": 1, "scores": 1 } },
    { $match: { "scores.type": "exam", "scores.score": { $gte: 95 } } },
    { $sort: { "scores.score": -1 } }
]);

```

```
db.userDetails.aggregate([
    {$match: {email:"nahidhassanbulbul@gmail.com"}},
    {$lookup: {
           from: "userDetails",
           localField: "email",
           foreignField: "refferBy",
           as: "reffer by me"
         }}
])
```
