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

Level 5 - Database Security - Cookies and Sessions
===


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
- bcrypt


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

Level 5 - Database Security - Cookies and Sessions - Code Example
===

Sessions cover storing client's information in server side about visiting a website. Cookies cover storing client's information on client side about a website. In general, heavy lifting is done on the Sessions file so larger files and info is stored in server side.

Session and cookie files are used for authentication and authorization of a user. Authentication is a process of verifying client's legitimacy. Authorization is a process of verifying what kind of specific page, data, info a user is able to access.

In this module, authentication and authorization topics covered in order to protect user specific data from outside world.

For creating session files, express-session middleware used. For authentication, passport is used.

Express is a framework for NodeJS, however express-session is a middlewawre for Express framework. A middleware strategy is built by someone on top of a framework. It is kind of community modding for gaming.

For more information, refer to "031_cookies-sessions_Level5".

NPM Package(s) Used:
- passport
- passport-local
- passport-local-mongoose
- express-session

```
//jshint esversion:6
// Load Node modules
const express = require('express');
const ejs = require('ejs');
// Initialize Express

const app = express();

// xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
// ==========================================
// ==========================================
const session = require('express-session');
// express-session is a middleware
const passport = require('passport');
// passport-local-mongoose salts and hashes user password
const passportLocalMongoose = require('passport-local-mongoose');
// passport-local is a dependency needed by passport-local-mongoose
// but we dont need to create a separate constant for that such as
// const passportLocal = require('passport-local');
// ==========================================
// ==========================================
// xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx

const bodyParser = require('body-parser');
const mongoose = require('mongoose');
app.use(bodyParser.urlencoded({extended: true}));

// Render static files
app.use(express.static(__dirname + '/public'));
// Set the view engine to ejs
app.set('view engine', 'ejs');

// xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
// ==========================================
// ==========================================
// This section will use express-session. It should be before all app.use methods
// but above mongoose connect.
app.use(session({
    // This secret will be stored in environmental variable.
    // You do not want to expose this to the public.
    // It means if the secret is invalid, then the session is invalid as well.
    secret: 'Our little secret.',
    resave: false,
    saveUninitialized: false,
    // After a day, the cookie will be deleted.
    cookie: {
        maxAge: 1000 * 60 * 60 * 24, // 1 day in total
        // Do not send secure true if it is not an https server.
        // secure: true,
    }
}));
app.use(passport.initialize());
app.use(passport.session());
// ==========================================
// ==========================================
// xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx

main().catch((err) => console.log(err));

async function main() {
    await mongoose.connect('mongodb://localhost:27017/cookiesSessionsDB', { useNewUrlParser: true });
}

const userSchema = new mongoose.Schema({
    email: String,
    password: String
});

// xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
// ==========================================
// ==========================================
// passport-local-mongoose is a Mongoose plugin.
userSchema.plugin(passportLocalMongoose);
// ==========================================
// ==========================================
// xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx

const User = mongoose.model('User', userSchema);

// xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
// ==========================================
// ==========================================
// This section comes from passport package
passport.use(User.createStrategy());
passport.serializeUser(User.serializeUser());
passport.deserializeUser(User.deserializeUser());
passport.serializeUser(function(user, done) {
    done(null, user);
});
passport.deserializeUser(function(user, done) {
    done(null, user);
});
// ==========================================
// ==========================================
// xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx


app.get('/', (req, res) => {
    res.render('home');
});

app.get('/login', (req, res) => {
    res.render('login');
});

app.get('/register', (req, res) => {
    res.render('register');
});

// xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
// ==========================================
// ==========================================
app.get('/logout', (req, res) => {
    // logout() method comes from passport package.
    req.logout();
    res.redirect('/');
});
app.get('/secrets', (req, res) => {
    if(req.isAuthenticated()) {
        res.render('secrets');
        // req.session comes from espress-session package.
        // It gives out info about session created in server side.
        console.log(req.session);
    } else {
        res.redirect('/login');
    }
});
app.post('/register', (req, res) => {
    User.register({username: req.body.username}, req.body.password, (err, user) => {
      if(err) {
          console.err(err);
          res.redirect('/register');
      } else {
          passport.authenticate('local')(req, res, () => {
            res.redirect('/secrets');
          });
      }
    });
});
// ==========================================
// ==========================================
// xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx

// Do not use findOne method with 2 parameters, this is not how it is documented.
// Use only email or password instead. See below app.post code for more details
// on how to use both values to validate if a user registered and entered correct password,
// used wrong password or never registered.

// app.post('/login', (req, res) => {
//     const email = req.body.username;
//     const password = req.body.password;

//     User.findOne({email: email, password: password}, (err, user) => {
//         if(err) {
//             console.error(err);
//         } else {
//             if(user){
//                 console.log('You are already registered!');
//                 res.render('secrets');
//             } else {
//                 console.error('You are never registered!');
//             }
//         }
//     });
// });

// xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
// ==========================================
// ==========================================
app.post('/login', (req, res) => {
    const user = new User({
    email: req.body.username,
    password: req.body.password 
    });

    // this method comes from passport
    req.login(user, (err) => {
        if(err) {
            console.error(err);
        } else {
            passport.authenticate('local')(req, res, () => {
                res.redirect('/secrets');
            });
        }
    });
});
// ==========================================
// ==========================================
// xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx

// Port website will run on
const port = 3000;
app.listen(port, function() {
	console.log(`Server is running on port ${port}`);
});
```

Level 6 - Database Security - 3rd Part Authorization, OAUTH - Code Example
===

For example if we have a web app, we can authorize and authenticate users to our website with the help of a third party account login, such as Google. This is even securer than previous methods because in this one, instead of relying on our server and our limited staff, we delegate this task to the bigger companies. It is also much easier for individuals to use their Google account to authenticate themselves into our website rather than creating a new account and password for our website as well.

GET (Stage 1): Our server sends a get request to Google server if the username and password the person enters matches the data.

POST (Stage 2): Google authenticates the user and sends over the authentication data to Our Server. Then we can add the email address that they entered in our database.


<p align="center">
  <img src="https://user-images.githubusercontent.com/32443904/138580584-c1f65a83-b643-4105-a08b-b7c3d66a2350.png" />
</p>

There are similar standards to OAuth but first understand why do we need this standard.

Why OAuth?
1) Granular Access Levels:
Your web app ask info to Google database in different levels. You might only need their profile and email address. If it is Facebook server that your web app requests info, you might want to request what their friends are just not to accidentally suggest their already existing friends as a friend on your web app.

2) Read Only or Read/Write Access:
Your web app might want to have Read/Write access to your database if it needs to do some specific task.

3) Revoke Access:
User can revoke the authorization that they granted on your website.

There are Steps to use OAuth
1) Step 1: Set up Your App in the developer console of bigger website in return we get an App ID (Client ID), App Secret (Client Secret) and we also need to setup a callback URI.
example"
Client ID: 30846982735492635854661
Client Secret: 317kajfgiaghakjglm71d1b20b8feab65a47dcb6b
Callback URI: http://localhost:3000/auth/google/secrets
2) Step 2: User Login Page. This page will take them to the actual website of bigger website so that the user can login.
3) Step 3: Our App asks permission after the user logins to their account on the bigger website.
4) Step 4: Now out server is able to receive Auth Code. If we want to go a step further, we can also exchange our Auth Code for an Access Token. Then we can save the Access Token into our database. The Access Token can be saved if we want pieces of info from the bigger website. The Access Token is valid longer than Auth Code. Access Token is like a one time pass whilst the Access Token can be perceived as a year pass.

NPM package Passport which is a middleware and the Passport strategy


NPM Package(s) Used:
- passport
- passport-local
- passport-local-mongoose
- express-session
- passport
- passport-google-oauth20
- passport-facebook
- mongoose-findorcreate

Get Permission from Google:
- Go to Google Credentials.
- Click on "CREATE CREDENTIALS" button then select "OAuth client ID".
- Select "External" and click on "CREATE" button.
- In the "OAuth consent screen" fill in the compulsory sections of the form. As an App Name I put "Google-OAuth2.0-Test".
- In the Scopes tab you specify the permissions that the user should grant your app. Select email, profile and openid. These are the very basic permission google give your website everytime you authenticate.
- Next, in the Test users tab, add the test users. I will add my email address as a test user.
- Then go to "Credentials" tab from the left hand side of the page. Click on "CREATE CREDENTIALS" button then select "OAuth client ID".
- Select "Web Application" and as an App Name put "Google-OAuth2.0-Test". For "Authorized JavaScript origins" put "http://localhost:3000". Whenever you change your server to an actual production server, you have to change this link. For "Authorized redirect URIs" put "http://localhost:3000/auth/google/secrets". Click on "CREATE" button.