Install Jekyll, NodeJS and Bundler.
Clone the forked repo on your machine
Enter the cloned folder via terminal and run bundle install
Then run bundle exec jekyll serve --config _config.yml,_config-dev.yml
Open it in your browser: http://localhost:4000
Test your app with bundle exec htmlproofer ./_site
