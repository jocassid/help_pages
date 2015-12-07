# The PythonAnywhere Help Pages

Indidividual help pages can be found in the "articles" folder.  They're in markdown format.  Pull requests are welcome!

*This repo contains the source files for help.pythonanywhere.com.  They are stored in markdown format and converted to html by the Nikola static site generator.*


## (for PA developers): pushing changes to live

A post-receive hook will take care of the nikola build and deploy

    git remote add live-site help@ssh.pythonanywhere.com:/home/help/barerepo.git
    # you'll probably want to add your public key to help's authorized_keys
    git push live-site master


## generating the help pages manually

For whatever reason...

    workon nikola
    nikola build
    nikola deploy  # nb this has a hard-coded path to a static files directory for the web app, so don't run it on your own pc

