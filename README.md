# dat-core

# Work in progress. Will be part of [dat](https://github.com/maxogden/dat) beta.

```
npm install dat-core
```

## Usage

``` js
var dat = require('dat-core')

var db = dat('./test')

db.put('hello', 'world', function () { // insert value
  db.get('hello', functoin (err, result) {
    console.log(result)   // prints result
    console.log(db.head) // the 'head' of the database graph (a hash)
  })
})
```

## API

#### `db = dat(path, [options])`

Create a new dat instance

#### `db.open([cb])`

Force open the dat. Otherwise it is opened lazily.

#### `db.head`

String containing the current head revision of the dat.
Everytime you mutate the dat this head changes.

#### `db.put(key, value, [options], [cb])`

Insert a value into the dat

#### `db.get(key, [options], cb)`

Get a value node from the dat

#### `db.del(key, [options], cb)`

Delete a node from the dat

#### `stream = db.createReadStream([options])`

Stream out values of the dat

#### `stream = db.createWriteStream([options])`

Stream in values to the dat

#### `stream = db.createPushStream([options])`

Create a replication stream that both pushes changes to another dat

#### `stream = db.createPullStream([options])`

Create a replication stream that both pulls changes from another dat

#### `stream = db.createSyncStream([options])`

Create a replication stream that both pulls and pushes

#### `stream = db.createChangesStream([options])`

Get a stream of changes happening to the dat. These changes
are ONLY guaranteed to be ordered locally.

#### `db.branches(callback)`

Get a list of branches in the dat.

Branches will happen if both you and a remote make changes to the dat
and you then pull the remote's changes.

They can also happen if you checkout a prevision revision and make changes.

#### `stream = db.compare([branch1, branch2, ...])`

Compare two or more branches with each other.
The stream will emit key,value pairs that conflict across the branches

#### `stream = db.merge([branch1, branch2, ...])`

Returns a merge stream. You should write key,value pairs to this stream
that conflicts across the branches (see the compare method above).

Once you end this stream the branches will be merged assuming the don't
contain conflicting keys anymore.

#### `anotherDat = db.checkout(ref)`

Checkout a branch of the dat or an older revision. This is useful if you want to pin your data
to a point in time.

``` js
db.put('hello', 'world', function () {
  var oldHash = db.head
  db.put('hello', 'verden', function () {
    var oldDb = db.checkout(oldHash)

    oldDb.get('hello', function (err, result) {
      console.log(result) // contains 'hello' -> 'world'
    })

    db.get('hello', function (err, result) {
      console.log(result) // contains 'hello' -> 'verden'
    })
  })
})
```

#### `dataset = db.dataset(name)`

Create a namespaced dataset. A dataset is a sub dat
that you can use to group datasets together

``` js
var salaries = db.dataset('salaries')

salaries.put('hello', 'world')
salaries.get('hello', function (err, result) {
  console.log(result) // contains world
})
```

## License

MIT
