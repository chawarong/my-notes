#### How to create a feature branch (from current master)

        $ git checkout -b master-chawarong
        $ git status
        $ git commit -m "experiment merge request" Gemfile
        $ git status
        $ git push origin master-chawarong

#### How to create a merge request from the web (so that the merge-request can be listed on Gitlab)

* Click on Project's Merge Requests > + New Merge Requests
* Select from branch and To branch
* Add Title, Assign to, and Submit merge request 

#### Perform accept merge automatically

* Add Discussion, Comments 
* If this is a temp branch, check "Remove source-branch"
* Click "Accept merge request" button


#### Perform accept merge manually

        $ git checkout master
        $ git status
        $ git fetch origin
        $ git merge origin/master-chawarong
        $ git status
        $ git log
        $ git push origin master

Note: next I should try merg request that conflict!!! - 30-Oct-2013


		git reset HEAD <files> to unstage merge failed file
		git status #the file moved from unmerge to modified
		git reset HEAD <files> to unstage modified file
		git checkout -- <files> to discard changes by copying original file 
		git status #the file should not be listed

#### for reference 
		git diff origin/master app/controllers/image_viewers_controller.rb
		git checkout --ours /path/to..
		git checkout --theirs /path/to..

#### reset all local changes
		git fetch origin
		git status
		git reset --hard origin/master






