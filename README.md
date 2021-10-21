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

In this method, a secret keyword is saved as an environmental variable by the programmer. This keyword is used by a special function to encrypt the passwords. As long as the secret keyword is not exposed to the hacker, person cannot decrypt the password.

Problem with this method is, if someone can hack into your system, it would not possibly be too hard to hack into your ".env" file and get your secret keyword to decrypt all passwords in the database.

For our example, imagine our secret keyword is "jsdhnicgvvtrbjjv". Imagine encryption is performed using AES-256-CBC with a random, unique initialization vector for each operation.

As you can see down below, in this method, the longer the plain password, longer the encrypted version gets.

An example of secret keyword and encrypted database is given down below.

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

This is a special type of function such as MD5 which converts normal password into a hashed version. Hash functions are super easy to encrypt but very hard to decrypt. It is like a one way ticket. So even if a hacker hacks into your database, they can't get the actual password of the people because decrypting the passwords is almost impossible due to the nature of the hashing functions.

But password encryption has a weakness. There are super easy passwords used by people just like in our imaginary database such as "qwerty" and "12345". MD5 is a very popular method to hash but it comes with a downside. A potential attacker can use a hash table which includes the MD5 or any other has functions and can determine the easy passwords. Remember that same password always create the same hash code. So if a person knows what is the equivalent of "qwerty" in MD5 hash function, he can expose all people who used the same password. As you can see "user@email.com" and "user4@email.com" has same hash results. Similarly, "user2@email.com" and "user5@email.com" has same hash results as well.

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

In password salting, a randomized combination added into the database. This randomized combination is concatenated into the existing password by NPM package "bcrypt". An example is given below.

Lets say "user@email.com" uses password "qwerty". Computer generates the random salting combination which is "abc123def456hij" for our example. Then, password gets concatenated with the salting combination by the system "qwertyabc123def456hij". Finally, this result is hashed and transformed into "58f044920877b4051a4d010c309bbbbe".

Hash rounds is adding more rounds to the hash. For instance, if you want to add another hash round, system takes the first round's hash and concatenates again with the salt "58f044920877b4051a4d010c309bbbbeabc123def456hij". By using this method, a new hash is generated "32a6ad50240bc42beced6b41aeeb72d5". This seems like a genius approach because every round makes the hash more complicated to be cracked with hash tables because the computing time increases exponentially.

For our example database, the password is only salted and hashed once.

Our imaginary database looks like this,

| User Email        | Salt              | Hash                                    |
| ----------------- |:-----------------:|:---------------------------------------:|
| user@email.com    | abc123def456hij   | 58f044920877b4051a4d010c309bbbbe        |
| user2@email.com   | xyz789def345rtg   | e4a3f07396962cafe54bfcfc218bd8be        |
| user3@email.com   | gyr363xgy849che   | 1fe4cf99092f738cf5058c6fab6c80ff        |
| user4@email.com   | ght624kyt130vbn   | 2235df64f79c8359c8718f7dff460489        |
| user5@email.com   | oyg870xrp432tgr   | a57816cf0eb1ed4d504295f76857b21a        |

Level 1 - Database Security - Password Strings - Code Example
===

For more information, refer to "027_plain-text-password_Level1".

```
app.post('/register', (req, res) => {
    const newUser = new User({
        email: req.body.username,
        password: req.body.password
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
    const password = req.body.password;

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

Level 2 - Database Security - Data Encryption - Code Example
===

For more information, refer to "028_password-encryption_Level2".

NPM Package(s) Used:
- mongoose-encryption

```
let encrypt = require('mongoose-encryption');

let secret = 'xxs4Ox4nSKSVnJzIxzy+es6ouOmoMcqcarAnEVRP26Q=';

userSchema.plugin(encrypt, {secret: secret, encryptedFields: ['password']});

app.post('/register', (req, res) => {
    const newUser = new User({
        email: req.body.username,
        password: req.body.password
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
    const password = req.body.password;

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

Level 3 - Database Security - Password Hashing - Code Example
===

For more information, refer to "029_password-hashing_Level3".

NPM Package(s) Used:
- md5

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

Level 4 - Database Security - Password Hashing and Salting, Salting Rounds - Code Example
===

For more information, refer to "030_password-hashing-salting_Level4".

NPM Package(s) Used:
- bycrypt


```
// password hashing and salting package
const bcrypt = require('bcrypt');
const saltRounds = 10;

app.post('/register', (req, res) => {

    bcrypt.hash(req.body.password, saltRounds, (err, hash) => {
        const newUser = new User({
            email: req.body.username,
            password: hash
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
});

app.post('/login', (req, res) => {
    const email = req.body.username;
    const password = req.body.password;

    User.findOne({email: email}, (err, user) => {
        if(err) {
            console.error(err);
        } else {
            if(user){
                bcrypt.compare(password, user.password, (err, result) => {
                    if(result === true) {
                        console.log('You are already registered!');
                        res.render('secrets');
                    } else {
                        console.error('Wrong password!');
                    }
                });
            } else {
                console.error('You are never registered!');
            }
        }
    });
});
```