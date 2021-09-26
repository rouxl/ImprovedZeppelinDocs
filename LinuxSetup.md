# Zeppelin Setup Guide By @Lando Calrissian#0001
![Zeppelin Banner](assets/zeppelinbanner.jpg)

**Consider putting a ⭐️ on this repo if you find it helpful!**

**Zeppelin self hosting. Its not too easy, but its also not impossible**

> THIS WILL BE GETTING REGULAR UPDATES, SO PLEASE BE PATIENT IF SOMETHING IS NOT RIGHT!

**Linux users, this is the guide for you!**
**if you are unsure on certain things, please send a messsage to the zeppelin support server (discord.gg/zeppelin)**

https://github.com/Dragory/ZeppelinBot/pull/232/files

Self-host Zeppelin

sudo apt update && sudo apt -y upgrade

sudo apt -y install mariadb-server git nano curl build-essential nginx screen

curl -sL https://deb.nodesource.com/setup_16.x | sudo bash -
sudo apt -y install nodejs

git clone https://github.com/Dragory/ZeppelinBot.git

ssh -T git@github.com

cd backend
npm ci
if github permissions error, change git+ssh to git+https in package-lock.json
if vips not found error, `sudo apt -y install libvips-dev`

cd ..
cp .env.example .env
nano .env
Fill in 32-char key

cd backend
cp bot.env.example bot.env
cp api.env.example api.env

sudo mysql_security_installation
	if sock error sudo /etc/init.d/mysql start
set root pw
sudo mariadb
grant all on *.* to 'root'@'localhost' identified by 'ENTER_YOUR_PASSWORD_HERE' with grant option;
grant all on zeppelin.* to 'zeppelin'@'localhost' identified by 'Zepp3l!n' with grant option;
flush privileges;
create database zeppelin;
exit

https://discord.com/developers/applications/
Create new application
Oauth2
	Redirects: http://localhost:8800/auth/oauth-callback
		(if remote access needed: http://[domain|ip]:8800/auth/oauth-callback)
	Copy Client ID, Client Secret
Bot
	Copy Token
	Privileged Gateway Intents: enable both
Invite bot to server

nano bot.env
	Token="BOT TOKEN"
	DB_HOST="localhost"
	DB_USER="root"
	DB_PASSWORD="YOUR_PASSWORD"
	DB_DATABASE="zeppelin"

nano api.env
	PORT="8800"
	CLIENT_ID="OAUTH CLIENT ID"
	CLIENT_SECRET="OAUTH CLIENT SECRET"
	OAUTH_CALLBACK_URL="http://localhost:8800/auth/oauth-callback" # replace locahost with domain on live server
		(if remote access needed: http://[domain|ip]:8800/auth/oauth-callback)
	DASHBOARD_URL="http://localhost:1234" # replace localhost with domain on live server
		
	DB_HOST="localhost"
	DB_USER="root"
	DB_PASSWORD="YOUR_PASSWORD"
	DB_DATABASE="zeppelin"
	STAFF=DISCORD_USERID,ID2,ID3

npm run build

npm run migrate-dev (development)
npm run migrate-prod (production)

sudo mariadb
use zeppelin;
INSERT INTO allowed_guilds (id, name, icon, owner_id) VALUES ("SERVER_ID", "SERVER_NAME", null, "OWNER_ID");
	Modify SERVER_ID, SERVER_NAME, OWNER_ID

INSERT INTO configs (id, `key`, config, is_active, edited_by)
VALUES (1, "global", "{\"prefix\": \"!\", \"owners\": [\"YOUR_ID\"]}", true, "YOUR_ID");
	Modify YOUR_ID X2

INSERT INTO configs (id, `key`, config, is_active, edited_by)
VALUES (2, "guild-GUILD_ID", "{\"prefix\": \"!\", \"levels\": {\"YOUR_ID\": 100}, \"plugins\": { \"utility\": {}}}", true, "YOUR_ID");
	Modify GUILD_ID, YOUR_ID X2

INSERT INTO api_permissions VALUES (GUILD_ID, YOUR_ID, "USER", "OWNER");
	Modify GUILD_ID, YOUR_ID

SET GLOBAL time_zone = '+0:00';

exit


(Start bot and api in development)
screen -dmS "zeppelinapi" npm run start-api-dev
screen -dmS "zeppelinbot" npm run start-bot-dev

or: npm run watch

(Start bot and api in production)
screen -dmS "zeppelinbot" npm run start-bot-prod
screen -dmS "zeppelinapi" npm run start-api-prod

(If using PM2)
npm i -g pm2

cd ..
pm2 start process-api.json
pm2 start process-bot.json

cd ../dashboard
npm ci
cp .env.example .env
nano .env
	API_URL="http://localhost:8800" #replace localhost with domain on live server

npm run build