# twilblog.github.io

This Week I Learned.

## adding new posts

Add new posts in markdown to `_posts` with this at the top of the file:

    ---
    layout: post
    title:  "You post title"
    date:   2015-07-28 10:27:24
    author: joebloggs
    categories: example another
    ---
    
The author (`joebloggs`) should reference a user in `_config.yml`, where you can have the following optional fields:

    joebloggs:
        display_name: Joe Bloggs
        avatar: https://joebloggs.png
        twitter: joebloggs
        github: jobloggs

# deps

    brew install ruby
    sudo gem install bundle
    sudo bundle install #from the root of this repo

To see your post before submitting pull request:

Install deps (assumig you have ruby + gem installed):

    sudo bundle install

Start local server:

    jekyll server
