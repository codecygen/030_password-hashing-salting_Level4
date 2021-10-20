In this project, instead of storing our passwords in database as it is, I will be using password hashing method
with an npm package "md5" and then salting with npm package "bycrypt". We can also set hash rounds with "bycrypt" to increase the computing time to hack a password with hash tables. This is a Level 4 security for storing password in database.

In cryptography, hashing is a much securer method than encryption. Because in encryption, if the hacker can hack into your system and gets your secret key for encryption, he can decode the password in your database. However, hashing does not need a secret key and it is a type of encryption by using complex mathematical operations. Encryption is easy but decoding is almost impossible.

Hashing a password is secure however it is not a fool proof method if used alone. Because hackers use a method called hash table.

Level 1 Security Database
===

This is the least secure type. Even though user3@email.com has set up a super strong password, it is still exposed to those who can hack into your database.


| User Email        | Password String                 |
| ----------------- |:-------------------------------:|
| user@email.com    | qwerty                          |
| user2@email.com   | 12345                           |
| user3@email.com   | 96"+F'_v=+.XwQ^c{x?2Mp8K'U6bD   |
| user4@email.com   | qwerty                          |
| user5@email.com   | 12345                           |


Level 3 Security Database - Password Hashing
===

NPM Package(s) Used:
md5

This is a special type of function such as MD5 which converts normal password into a hashed version. Hash functions are super easy to encrypt but very hard to decode. It is like a one way ticket. So even if a hacker, hacks into your database, they can still not get the actual password of the people because decoding the passwords is almost impossible due to the nature of hashing functions.

But it has a weakness. There are super easy passwords that are used in this database just like any database such as "qwerty" and "12345" and MD5 is a very popular method to hash, hacker can use a hash table which includes the MD5 or any other functions hashes of the easy passwords. Remember that same password always create the same hash version. So if a person knows what is the equivalent of "qwerty", he can expose all people. who has same password in database. As you can see "user@email.com" and "user4@email.com" has same hash results. Similarly, "user2@email.com" and "user5@email.com" has same hash results as well.

| User Email        | Hash                                    |
| ----------------- |:---------------------------------------:|
| user@email.com    | d8578edf8458ce06fbc5bb76a58c5ca4        |
| user2@email.com   | 827ccb0eea8a706c4c34a16891f84e7b        |
| user3@email.com   | eeae00fa9767afd31155b672ccab9c75        |
| user4@email.com   | d8578edf8458ce06fbc5bb76a58c5ca4        |
| user5@email.com   | 827ccb0eea8a706c4c34a16891f84e7b        |


Level 4 Security Database - Password Hashing and Salting - Salting Rounds
===

NPM Package(s) Used:
md5
bycrypt

In password salting, a randomized combionation number added into the database. This randomized combination is concatenated into the existing password by NPM package "bycrypt". An example is given below.

| User Email        | Salt              | Hash                                    |
| ----------------- |:-----------------:|:---------------------------------------:|
| user@email.com    | abc123def456hij   | 58f044920877b4051a4d010c309bbbbe        |



| User Email        | Salt              | Hash                                    |
| ----------------- |:-----------------:|:---------------------------------------:|
| user@email.com    | abc123def456hij   | 58f044920877b4051a4d010c309bbbbe        |
| user2@email.com   | xyz789def345rtg   | e4a3f07396962cafe54bfcfc218bd8be        |
| user3@email.com   | gyr363xgy849che   | 1fe4cf99092f738cf5058c6fab6c80ff        |
| user4@email.com   | ght624kyt130vbn   | 2235df64f79c8359c8718f7dff460489        |
| user5@email.com   | oyg870xrp432tgr   | a57816cf0eb1ed4d504295f76857b21a        |


md5
===

It is one of the most renowned mathematical function that is used for password hashing.

```
const md5 = require('md5');

// Then use wherever you need to has the password..
// const password = md5(mypassword);

app.post('/register', (req, res) => {
    const newUser = new User({
        email: req.body.username,
        password: md5(req.body.password)
    });

    newUser.save((err) => {
        if(err){
            console.error(err);
        } else {
            console.log('New user added!');
            res.render('secrets');
        }
    });
});

app.post('/login', (req, res) => {
    const email = req.body.username;
    const password = md5(req.body.password);

    User.findOne({email: email}, (err, user) => {
        if(err) {
            console.error(err);
        } else {
            if(user){
                if(user.password === password) {
                    console.log('You are already registered!');
                    res.render('secrets');
                } else {
                    console.error('Wrong password!');
                }
            } else {
                console.error('You are never registered!');
            }
        }
    });
});
```