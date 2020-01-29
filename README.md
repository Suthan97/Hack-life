# sef-github-leaderboad


# Setup Development Environment

## Setting up Dependencies

#### Install tomcat 9
[Ubuntu](https://www.digitalocean.com/community/tutorials/install-tomcat-9-ubuntu-1804)

[macOs](https://medium.com/@fahimhossain_16989/installing-apache-tomcat-on-macos-mojave-using-homebrew-28ce039b4b2e)

## Configuring the database

( Upgade to MySQL 8.0 if you are using lower versions )

1. Create new mysql user for sef
    ```sql
    CREATE USER 'sef'@'localhost' IDENTIFIED BY 'sef123';
    ```
2. Grant access to the new user
    ```sql
    GRANT ALL PRIVILEGES ON *.* TO 'sef'@'localhost';
    ```
3. Run the mysql.sql script in scripts directory to import the database.
    ```
    mysql> source scripts\mysql.sql;
    ```

## Building the project

1. Create a copy from both example files.
    ```
        cp api/src/main/resources/application.properties.example api/src/main/resources/application.properties
        cp api/src/main/resources/configprops.properties.example api/src/main/resources/configprops.properties
    ```

1. Replace the respective properties in the application.properties file
    ```
     vim api/src/main/resources/application.properties  
    ```
   - `<DB_NAME>` - Database name 
   - `<DB_USERNAME>` - Database name 
   - `<DB_PASSWORD>` - Database name 

1. Add a random String as the webhook key in configprops.properties file
   ```
     vim api/src/main/resources/configprops.properties  
   ```
    replace `<WEBHOOK_KEY>` key with your random string. 
2. Build the project using maven
    ```
    mvn clean install
    ```
3. Copy generated war files to tomcat webapps directory
    ```
    cp ui/target/github-leaderboard.war <tomcat_directory>/webapps
    cp api/target/api\#github-leaderboad\#v1.war   <tomcat_directory>/webapps
    ```
4. Access the UI by visiting `http://localhost:8080/github-leaderboard/`

## Setting up the webhook

Now that our webhook is ready to deliver messages.   

Recall that we specifically set our webhook URL to `http://localhost:8080/api/github-leaderboad/v1/webhook/{YOUR_KEY}`. 
Since we're developing locally, we'll need to expose our local development environment to the Internet, so that GitHub 
can send out messages, and our local server can process them.

### Using ngrok

First, we'll install a program to expose our local host to the Internet. We'll use ngrok to do this. 
[ngrok is a free download](https://ngrok.com/download) available for all major operating systems.

When you're done with that, you can expose your localhost by running `./ngrok http 8080` on the command line. 
You should see a line that looks something like this:
```
Forwarding    http://7e9ea9dc.ngrok.io -> 127.0.0.1:8080
```

Copy that funky `*.ngrok.io` URL! We're now going to go back to the Payload URL and pasting this server into that field. 
It should look something like `http://7e9ea9dc.ngrok.io/api/github-leaderboad/v1/webhook/{YOUR_KEY}`. It is our webhook 
URL.

By doing this, we've set ourselves up to expose our `localhost` at path `/api/github-leaderboad/v1/webhook/{YOUR_KEY}` 
to the Internet.

### Adding webhook to github

To set up a webhook, go to the settings page of your repository or organization. From there, click Webhooks, 
then Add webhook.

`Payload URL` - Our Webhook url

`Content type` - `application/json`

`Which events would you.. webhook?` - Select 'Let me select individual events.' & select `Pull requests`

Now send a pull request to your repo and merge to test.

Rejoice!!! 
