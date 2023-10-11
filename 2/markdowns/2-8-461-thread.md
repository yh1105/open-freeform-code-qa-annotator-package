
# Post \#60026385 [Link](https://stackoverflow.com/questions/60026385/)

## Bulk insert with sqlx

**Vote**: 3 (575/702) **Views**: 14051 (371/702) 

**Internal ID** \#2-8-461

Created at 2020-02-02 13:05:52

Tags: `postgresql` `go` `sqlx`

----------

#### Metadata:

Area: `Back-end`

Language: `go` (full parsed tag list: `go` `sql`)

----------

**Notepad**


----------

I'am trying to make a bulk insert with sqlx and golang : 

```
for _, result := range results {
            queryInsert := `INSERT INTO "DataCom_travel" (com1,com2,path,time) VALUES ($1,$2,$3,$4)`
            db.MustExec(queryInsert,result.Com1,result.Com2,result.Path,result.Time)
            queryUpdate := `UPDATE "DataCom_commcombinaison" set done = TRUE WHERE com1 =$1 and com2 =$2`
            db.MustExec(queryUpdate,result.Com1,result.Com2)
        }
```


That code works but it's slow.

I've tried this :

```
tx := db.MustBegin()
for _, result := range results {
        queryInsert := `INSERT INTO "DataCom_travel" (com1,com2,path,time) VALUES ($1,$2,$3,$4)`
        tx.MustExec(queryInsert,result.Com1,result.Com2,result.Path,result.Time)
        queryUpdate := `UPDATE "DataCom_commcombinaison" set done = TRUE WHERE com1 =$1 and com2 =$2`
        tx.MustExec(queryUpdate,result.Com1,result.Com2)
    }
tx.Commit()
```


but it does nothing when i look at my records i doesn't see any records.

Regards

edit : 

```
INSERT INTO "DataCom_travel" (com1,com2,path,time) VALUES ($1,$2,$3,$4),($2,$3,$4,$5),($3,$4,$5,$6),($4,$5,$6,$7),($5,$6,$7,$8),($6,$7,$8,$9),($7,$8,$9,$10),($8,$9,$10,$11),($9,$10,$11,$12),($10,$11,$12,$13)
UPDATE "DataCom_commcombinaison" set done = TRUE WHERE (com1 = $1 AND com2 = $2 )  OR (com1 = $2 AND com2 = $3 )  OR (com1 = $3 AND com2 = $4 )  OR (com1 = $4 AND com2 = $5 )  OR (com1 = $5 AND com2 = $6 )  OR (com1 = $6 AND com2 = $7 )  OR (com1 = $7 AND com2 = $8 )  OR (com1 = $8 AND com2 = $9 )  OR (com1 = $9 AND com2 = $10 )  OR (com1 = $10 AND com2 = $11 ) 
panic: pq: column "com2" is of type integer but expression is of type text
```



----------
        
## Answer \#0

**Accepted** Vote: 7

Created at 2020-02-02 13:42:53

------------

You could use the loop to just build a single insert and aggregate the parameters into a slice and then execute the query just once. Something like this:

```
queryInsert := `INSERT INTO "DataCom_travel" (com1,com2,path,time) VALUES `
queryUpdate := `UPDATE "DataCom_commcombinaison" set done = TRUE WHERE `

insertparams := []interface{}{}
updateparams := []interface{}{}

for i, result := range results {
    p1 := i * 4 // starting position for insert params
    p2 := i * 2 // starting position for update params

    queryInsert += fmt.Sprintf("($%d,$%d,$%d,$%d),", p1+1,p1+2,p1+3,p1+4)
    queryUpdate += fmt.Sprintf("(com1=$%d AND com2=$%d) OR ", p2+1, p2+2)

    insertparams = append(insertparams, result.Com1, result.Com2, result.Path, result.Time)
    updateparams = append(updateparams, result.Com1, result.Com2)
}

queryInsert = queryInsert[:len(queryInsert)-1] // remove trailing ","
queryUpdate = queryUpdate[:len(queryUpdate)-4] // remove trailing " OR "

db.MustExec(queryInsert, insertparams...)
db.MustExec(queryUpdate, updateparams...)
```



------------
    
    
## Answer \#1

 Vote: 6

Created at 2022-03-11 20:26:17

------------

You can use `NamedExec` instead of string concatenation. There's an example at the bottom of sqlx README: [https://github.com/jmoiron/sqlx/blob/master/README.md](https://github.com/jmoiron/sqlx/blob/master/README.md)


------------
    
    
## Answer \#2

 Vote: 3

Created at 2021-01-08 23:18:33

------------

It may be useful to generate the query with another library, and then use sqlx to execute the query.
For example, using [squirrel](https://github.com/Masterminds/squirrel):
```
// Build query with Squirrel
q := squirrel.Insert("DataCom_travel").Columns("com1", "com2", "path", "time")
for _, result := range results  {
  q = q.Values(result.Com1, result.Com2, result.Path, result.Time)
}
sql, args, err := q.ToSql()
if err != nil {
  return err
}

// Execure query with sqlx
res, err := db.MustExec(sql, args...)
```



------------
    
    