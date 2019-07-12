# redmine
Bastille Template to create a Redmine Jail


Once jail is started attach to console and run the following setup steps:

First test your mysql server by logging in.

	mysql -u root

Once logged in run the following to create the database schema:

	CREATE DATABASE redmine CHARACTER SET utf8;
	CREATE DATABASE redmine_dev CHARACTER SET utf8;
	CREATE DATABASE redmine_test CHARACTER SET utf8;

	CREATE USER 'redmine'@'localhost' IDENTIFIED BY 'YourRedminePassword';

	GRANT ALL PRIVILEGES ON redmine.* TO 'redmine'@'localhost';
	GRANT ALL PRIVILEGES ON redmine_dev.* TO 'redmine'@'localhost';
	GRANT ALL PRIVILEGES ON redmine_test.* TO 'redmine'@'localhost';
	FLUSH PRIVILEGES;

Edit /usr/local/www/redmin/config/database.yml


	production:
	  adapter: mysql2
	  database: redmine
	  host: localhost
	  username: redmine
	  password: YourRedminePassword
	  encoding: utf8

	test:
	  adapter: mysql2
	  database: redmine_test
	  host: localhost
	  username: redmine
	  password: YourRedminePassword
	  encoding: utf8

	development:
	  adapter: mysql2
	  database: redmine_dev
	  host: localhost
	  username: redmine
	  password: YourRedminePassword
	  encoding: utf8


Now I recommend you run mysql_secure_installation to harden your mysql install.


Next run the following ruby and permission commands:

	cd /usr/local/www/redmine/
	bundle install --without development test
	bundle exec rake generate_secret_token
	setenv RAILS_ENV production
	setenv REDMINE_LANG en
	bundle exec rake db:migrate
	bundle exec rake redmine:load_default_data
	mkdir -p tmp tmp/pdf public/plugin_assets
	chown -R www:www files log tmp public/plugin_assets
	chmod -R 755 files log tmp public/plugin_assets
	find files log tmp public/plugin_assets -type f -exec chmod -x {} +

	bundle exec rails server webrick -e production
