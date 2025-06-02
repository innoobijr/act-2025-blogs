# Adjoint School Blogs

**Short code**: Each blog post pair will have a short code associated to their blog post. For example Théo and Onkar blog has the short code `1b-theo-onkar`. This short code refers to two things: (1) the subdirectory that contains your blog posts and (2) the name of the branch that will contains the changes to your blog post.

If you have sent me your email, you should be able to clone, checkout into your branch and push changes to this repo. See the instructions below. 

## 1. Clone the repo
```git clone https://github.com/innoobijr/act-2025-blogs.git```

## 2. Checkout to the branch containing your blog short code
For example, Théo or Onkar could do the following:
* A. Enter the directory
```cd act-2025-blogs```
* B. Checkout into your branch
```git checkout 1b-theo-onkar```

You can now start making changes to your blog post in this repo. Once you have made changes that you want to share with us. You need to add the new files and commit your changes.
## 3. Pushing changes
Assuming you are in the fold containing your blog post (i.e in the case of Théo and Onkar this would be `act-2025-blogs/1b-theo-onkar`, you will need to:
* A. Add new files
   ```git add  .```
* B. Commit the changes
  ```git commit -am "<whatever commit message you want ot add>" ```
* B. Push to change to **YOUR** branch
  For Théo and Onkar this would be:
  ``` git push -u origin 1b-theo-onkar```

When in doubt just message us on Zulip.
