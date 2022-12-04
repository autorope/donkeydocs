Thank you for contributing to the Donkeycar projects.  The documentation is critical for the success of your users so we appreciate you contributions.  Accuracy and completeness is critical. Many users are beginners so please write your contributions with this in mind; don't assume that 'they should already know that'.  

We use the [mkdocs package](https://www.mkdocs.org/user-guide/) to create the html for the https://docs.donkeycar.com site.  The files in repo are in markdown format; mkdocs `compiles` those to html so they can be displayed in a browser.  You make your changes in your own fork of the donkeydocs repo and open a pull request so it can be merged into the main donkeydocs repo.  Once the PR is merged then the changes will automatically be compiled and pushed to the https://docs.donkeycar.com site.   

 1. Fork the [donkeydocs](https://github.com/autorope/donkeydocs) repo in your own github account.
 2. Clone your fork to your computer so you have a local copy that can be changed.
 3. Create a new branch in the clone of your fork; the branch will be used to make the changes/additions.  If this is related to an issue in the main repo then start the name of the branch with the issue number.
 4. Make the changes/additions and check them in your fork.  We use a package called mkdocs to compile the markdown files that you edit/create into html. See the [mkdocs documentation](https://www.mkdocs.org/user-guide/writing-your-docs/#writing-with-markdown) for the particulars of the markdown format that it uses.  If you install mkdocs you can use it to generate a live preview so you can see the changes as you save them.
   - open a console (git bash console on windows) and cd into the root of your cloned donkeydocs project folder.
   - create a python virtual environment and activate it.
   - install the mkdocs package into the activated virtual environment.
   - run the mkdocs server.  This will provide you with a url that you can open in your browser to see the rendered docs.  
     ```
     python3 -m venv env
     source env/bin/activate
     pip3 install mkdocs
     mkdocs serve
     ```
   - On subsequent edit sessions, just reactivate the virtual environment and start the mkdocs server (you don't need to recreate the environment, just reactivate it).

 5. Once you are done making changes/addtions in your branch, commit the changes and push them to your forked repo.  If you find that you need to make more changes then just rinse and repeat; make changes, commit them, push them.
 6. Once you are sure your updates are correct and they are pushed to your forked repo, open a pull request.  Because you created a fork of the main donkeydocs repo, you can reflect this pull request in the main repo.  Github has a nice feature; after you push to your forked repo; if you go to the repo in github.com then you will see a green `Compare & Pull Request` button; you can push that to create your pull request.
 7. That will open a pull request in the main donkeydocs repo.  I would alert the discord maintainers channel that you have opened a PR so someone will review it.  You can expect a couple of rounds of comments during the PR process.  If you get a change request then you can make changes related to those comments and push them; the new changes will be reflected in the pull request.

This process is documented in more detail here https://docs.github.com/en/get-started/quickstart/contributing-to-projects
