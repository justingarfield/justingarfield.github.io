## For WSL Bash

### Install Ruby / Deps / Jekyll

* BrightBox PPA was broken and 404
* See: [https://jekyllrb.com/docs/installation/ubuntu/](Jekyll on Ubuntu)

```bash
sudo apt-get install ruby-full build-essential zlib1g-dev gh

echo '# Install Ruby Gems to ~/gems' >> ~/.bashrc
echo 'export GEM_HOME="$HOME/gems"' >> ~/.bashrc
echo 'export PATH="$HOME/gems/bin:$PATH"' >> ~/.bashrc
source ~/.bashrc

gem install jekyll bundler

gh auth login
gh repo clone justingarfield.github.io
cd justingarfield.github.io

jekyll new --skip-bundle .

sed -i '/^[^#]/ s/\(^.*gem "jekyll", "~> .*$\)/#\ \1/' Gemfile

# See https://pages.github.com/versions/ for github-pages gem version (currently 228)
sed -i 's/.*# gem "github-pages", group: :jekyll_plugins.*/gem "github-pages", "~> 228", group: :jekyll_plugins/' Gemfile

bundle install
bundle add webrick

rougify style github > style.css

bundle exec jekyll serve
```

Navigate to: http://127.0.0.1:4000/

Upgrade Gems over-time

```bash
bundle update github-pages
```
