# SYNOPSIS
Store and get users. Salt their passwords, persist them to disk.

# USAGE
Pass a leveldb instance to the `Users` constructor. I recommend using
[`sublevel`][0] to bucket your database's meta data and [`multilevel`][1]
if your database is on the network. See [`level-user`][2] for client side
support.

```js
var db = level('./db')
var users = Users(db)
```

# API

### `create`
Create a new user by passing a user object. Only `username` is required.
When a new user is created, a uuid is returned.

```js
var user = {
  username: 'test',
  password: 'pass',
  foo: 100,
  email: 'test@tap.com'
}

users.create(user, function(err, id) {
  // id => 'a3a1d270-75fe-4bfc-a2bc-e358903bc540'
})
```

### `remove`
Removes a user and any indexs that have been created for their records

```js
users.remove(id, function(err) {
})
```

### `addIndexes`
Add indexes that can be used to `get` a user. You can use any arbitrary 
field that is in your user object. In this example we index on `email`.

```js
users.addIndexes(['email'], function(err) {
})
```

### `get`
Get a user by their uuid. Returns the user object and a put method that
can be used to update the user data.

```js
users.get(id, function(err, user, put) {
})
```

Here's an example using an index. returns the user's id.

```js
users.get({ email: 'test@tap.com' }, function(err, id) {
})
```

### `auth`
Find out if the proposed password matches with the a salt stored for a 
given user id. If auth is successful, you get a the user object and
a put function so that you can write some new user information. If the
auth fails, the `user` object will be null.

```js
users.auth(id, password, function(err, user, put) {
  user.isCool = true
  put(user, function(err) {
    // ...
  })
})
```

### `addGroups`
Add groups to a user, this is just sugar for array index checking.

```js
users.addGroups(id, ['super','rooty'], function(err, user, put) {
})
```

### `removeGroups`
Remove groups from a user, also just sugar for array index checking.

```js
users.removeGroups(id, ['rooty'], function(err, user, put) {
})
```

[0]:https://github.com/dominictarr/level-sublevel
[1]:https://github.com/juliangruber/multilevel
[2]:https://github.com/maxogden/level-user
