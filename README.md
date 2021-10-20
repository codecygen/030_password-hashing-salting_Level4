In this project, instead of storing our passwords in database as it is, I will be using password hashing method
with an npm package "md5". This is a Level 3 security for storing password in database.

In cryptography, hashing is a much securer method than encryption. Because in encryption, if the hacker can hack into your system and gets your secret key for encryption, he can decode the password in your database. However, hashing does not need a secret key and it is a type of encryption by using complex mathematical operations. Encryption is easy but decoding is almost impossible.

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