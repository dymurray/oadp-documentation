# Running mdBook

The OADP docs book is served using [mdBook](https://github.com/rust-lang-nursery/mdBook). If you want to test changes to the book locally, follow these directions:

1. Follow the instructions at [https://github.com/rust-lang-nursery/mdBook#installation](https://github.com/rust-lang-nursery/mdBook#installation) to
  install mdBook.
1. Run `mdbook serve`
1. Visit [http://localhost:3000](http://localhost:3000)

# Steps to deploy

There are no manual steps needed to deploy the website.

OADP book website is deployed on Netlify.
There is a preview of the website for each PR.
As soon as the PR is merged, the website will be built and deployed on Netlify.
