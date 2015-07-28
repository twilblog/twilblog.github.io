# twilblog.github.io
This Week I Learned.

## adding new posts

Add new posts in markdown to `_posts` with this at the top of the file:

    ---
    layout: post
    title:  "You post title"
    date:   2015-07-28 10:27:24
    author: John Smith
    author_avatar: http://somepathtoyouravatarthatisnthuge.com/small.gif
    categories: example another
    ---
    
`author_avatar` is optional.


To see your post before submitting pull request:

Install deps (assumig you have ruby + gem installed):

    sudo bundle install

Start local server:

    jekyll server
