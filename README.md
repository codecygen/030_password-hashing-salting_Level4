In this project, instead of storing our passwords in database as it is, I will be using password hashing method
with an npm package "md5" and then salting with npm package "bycrypt". We can also set hash rounds with "bycrypt" to increase the computing time to hack a password with hash tables. This is a Level 4 security for storing password in database.

In cryptography, hashing is a much securer method than encryption. Because in encryption, if the hacker can hack into your system and gets your secret key for encryption, he can decode the password in your database. However, hashing does not need a secret key and it is a type of encryption by using complex mathematical operations. Encryption is easy but decoding is almost impossible.

Hashing a password is secure however it is not a fool proof method if used alone. Because hackers use a method called hash table.

Level 1 - Database Security - Password Strings
===

This is the least secure type. Even though user3@email.com has set up a super strong password, it is still exposed to those who can hack into your database. Hackers can easily recover passwords of individuals and since people tend to use same passwords in different websites, hackers can possibly hack into their bank accounts or other sensitive data in different websites.

An example database is given down below.


| User Email        | Password String                 |
| ----------------- |:-------------------------------:|
| user@email.com    | qwerty                          |
| user2@email.com   | 12345                           |
| user3@email.com   | 96"+F'_v=+.XwQ^c{x?2Mp8K'U6bD   |
| user4@email.com   | qwerty                          |
| user5@email.com   | 12345                           |

Level 2 - Database Security - Data Encryption
===

NPM Package(s) Used:
- mongoose-encryption

In this method, a secret keyword is saved as an environmental variable by the programmer. This keyword is used by a special function to encrypt the passwords. As long as the secret keyword is not exposed to the hacker, person cannot decode the password.

Problem with this method is, if someone can hack into your system, it would not possibly be that hard to hack into your ".env" file and get your secret keyword to decode all passwords in the database.

For our example, imagine our secret keyword is "jsdhnicgvvtrbjjv". Imagine encryption is performed using AES-256-CBC with a random, unique initialization vector for each operation.

As you can see down below, the longer the password, longer the password gets.

An example secret keyword and database is given down below.

| Secret Keyword (Saved into .env file)        |
| -------------------------------------------- |
| jsdhnicgvvtrbjjv                             |

| User Email        | Encrypted Password                               |
| ----------------- |:------------------------------------------------:|
| user@email.com    | EY7kBsNnWBy6vBmS2Mvzuw==                         |
| user2@email.com   | yiFA2xQ1gwZJJ7s1k7zG6w==                         |
| user3@email.com   | dfb0b565229cf08cafd07952b442bb6b                 |
| user4@email.com   | EY7kBsNnWBy6vBmS2Mvzuw==                         |
| user5@email.com   | yiFA2xQ1gwZJJ7s1k7zG6w==                         |

Level 3 - Database Security - Password Hashing
===

NPM Package(s) Used:
- md5

This is a special type of function such as MD5 which converts normal password into a hashed version. Hash functions are super easy to encrypt but very hard to decode. It is like a one way ticket. So even if a hacker, hacks into your database, they can still not get the actual password of the people because decoding the passwords is almost impossible due to the nature of hashing functions.

But it has a weakness. There are super easy passwords that are used in this database just like any database such as "qwerty" and "12345" and MD5 is a very popular method to hash, hacker can use a hash table which includes the MD5 or any other functions hashes of the easy passwords. Remember that same password always create the same hash version. So if a person knows what is the equivalent of "qwerty", he can expose all people. who has same password in database. As you can see "user@email.com" and "user4@email.com" has same hash results. Similarly, "user2@email.com" and "user5@email.com" has same hash results as well.

An example database is given down below.

| User Email        | Hash                                    |
| ----------------- |:---------------------------------------:|
| user@email.com    | d8578edf8458ce06fbc5bb76a58c5ca4        |
| user2@email.com   | 827ccb0eea8a706c4c34a16891f84e7b        |
| user3@email.com   | eeae00fa9767afd31155b672ccab9c75        |
| user4@email.com   | d8578edf8458ce06fbc5bb76a58c5ca4        |
| user5@email.com   | 827ccb0eea8a706c4c34a16891f84e7b        |


Level 4 - Database Security - Password Hashing and Salting, Salting Rounds
===

NPM Package(s) Used:
- md5
- bycrypt

In password salting, a randomized combination added into the database. This randomized combination is concatenated into the existing password by NPM package "bycrypt". An example is given below.

Let's say "user@email.com" uses password "qwerty". Computer generates the random salting combination which is "abc123def456hij" for our example. Then, password gets concatenated by the system "qwertyabc123def456hij". Then this result is hashed and transformed into "58f044920877b4051a4d010c309bbbbe".

Hash rounds is adding more rounds to the hash. Let's say if you want to add another hash round, system takes the first round's hash and concatenates again with the salt "58f044920877b4051a4d010c309bbbbeabc123def456hij". With this method, new hash is generated "32a6ad50240bc42beced6b41aeeb72d5". This is genius because every round makes the hash more complicated to be cracked with hash tables because the computing time increases exponentially.

For our example, the password is only salted once (single round) down below.

For a single round salted hash, our imaginary database looks like this,

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