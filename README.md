[![Build Status](https://travis-ci.org/codeforamerica/promptly.png?branch=master)](https://travis-ci.org/codeforamerica/promptly)

# Promptly
Promptly is a text message notification system originally built by 2013 Code for America fellows for the San Francisco Human Services Agency. See [promptly.io](http://promptly.io) for more info on the project.

### Contribute
This project is young and in flux. Feel free to email sf@codeforamerica.org if you're interested in deploying or contributing.

### Requirements
Promptly is a Ruby on Rails application that uses Twilio to send text messages.

If you need help setting up RVM, Ruby, and all that stuff, we recommend [Moncef's](http://about.me/moncef) great tutorial on [setting up a Mac for development with Xcode, Homebrew, Git, RVM & Ruby](http://www.moncefbelyamani.com/how-to-install-xcode-homebrew-git-rvm-ruby-on-mac/).

You'll also need to setup a [Twilio account](https://www.twilio.com/) and buy a Twilio phone number. You can get a development phone number for free but you'll need to pay to send text messages. [Check our their pricing](https://www.twilio.com/sms/pricing).

### Install locally
1) Install Bundler
```sh
$ gem install bundler
```

2) Install the Heroku Toolbelt: [https://toolbelt.heroku.com/](https://toolbelt.heroku.com/)

3) Install PostgreSQL
- Mac: http://postgresapp.com/
- Other: http://www.postgresql.org/

4) Clone this repo
```sh
$ git clone https://github.com/codeforamerica/promptly.git
```

5) Install required gems
```sh
$ cd promptly
$ bundle install
```

6) Configure environment variables by renaming **.sample_env** to **.env** and filling in the values.

Promptly requires the following environment variables:
- TWILIO_NUMBER is your Twilio phone number (just numbers, no punctuation)
- TWILIO_SID is your Twilio account SID 
- TWILIO_TOKEN is your twilio auth token
- SECRET_TOKEN is used to prevent cookie tampering. Run `$ rake secret` to get a pseudo-random key to use.

*Confirm your .gitignore includes /.env so you don't publicize these keys!*

7) Create **config/database.yml**
```ruby
# PostgresApp creates a default user $USER with no password.
# Otherwise you'll have to make one yourself.

development:
  adapter: postgresql
  host: localhost
  username: [PostgreSQL username] 
  database: promptly
```

8) Setup database
```sh
$ rake db:setup

# Equivalent to:
# rake db:create
# rake db:schema:load
# rake db:seed
```

9) Start the server
```sh
$ foreman start
```
You should see the project at <a href="http://localhost:5000">http://localhost:5000</a>

You can sign in with email=**admin@example.com** and pass=**administrator**.

### Deploy to Heroku
[Signup for Heroku](https://id.heroku.com/signup) if you havn't already.

1) Create a Heroku app and push your code
```sh
$ heroku login
$ heroku create
$ git push heroku master
```

2) Add the Heroku PostgreSQL addon and promote it
```sh
$ heroku addons:add heroku-postgresql
$ heroku pg:promote HEROKU_POSTGRESQL_[YOUR COLOR]_URL
```

3) Configure Heroku environment variables
```sh
$ heroku config:add TWILIO_NUMBER=[your Twilio phone number]
```
Repeat for TWILIO_SID, TWILIO_TOKEN, and SECRET_TOKEN using the values from your .env file.

4) Load the Promptly schema
```sh
$ heroku run rake db:setup
```

At this point you can run `$ heroku open` to see the project.

5) Add the Heroku scheduler addon

For annoying reasons we won't go into here, Promptly requires a periodic rake task to update the conversations model with incoming text messages from users. You can do this on Heroku using the scheduler addon.

```sh
$ heroku addons:add scheduler
$ heroku addons:open scheduler
```
Run the task `rake update_conversations` every 10 minutes:
![heroku scheduler addon](http://codeforamerica.github.io/promptly/heroku-scheduler-addon.png)

6) Change the admin password
```sh
$ heroku open
```

Sign in with email=**admin@example.com** and pass=**administrator**. Click Welcome, Admin in the top right and update your password.

And you're all set! Run  to schedule your first reminder. 

<a href="#"><img src="https://a248.e.akamai.net/camo.github.com/e8ce7fcd025087eebe85499c7bf4b5ac57f12b1e/687474703a2f2f73746174732e636f6465666f72616d65726963612e6f72672f636f6465666f72616d65726963612f6366615f74656d706c6174652e706e67" alt="codeforamerica"/></a>