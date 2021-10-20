In this project, instead of storing our passwords in database as it is, I will be using password hashing method
with an npm package "md5" and then salting with npm package "bycrypt". We can also set hash rounds with "bycrypt" to increase the computing time to hack a password with hash tables. This is a Level 4 security for storing password in database.

In cryptography, hashing is a much securer method than encryption. Because in encryption, if the hacker can hack into your system and gets your secret key for encryption, he can decode the password in your database. However, hashing does not need a secret key and it is a type of encryption by using complex mathematical operations. Encryption is easy but decoding is almost impossible.

Hashing a password is secure however it is not a fool proof method if used alone. Because hackers use a method called hash table.

| Tables        | Are           | Cool  |
| ------------- |:-------------:| -----:|
| col 3 is      | right-aligned | $1600 |
| col 2 is      | centered      |   $12 |
| zebra stripes | are neat      |    $1 |


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