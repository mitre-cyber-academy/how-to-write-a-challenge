# How to write a challenge:

Our game instantiation program uses a config file and a folder full of challenge recipes to stand up a game infrastructure quickly and easily. In order to write a challenge thats usable by our program, it must be in a specific format. 

A sample directory structure for a challenge is shown below:

    some_challenge_name  
    ├── cookbooks                     #holds the server "recipes" that the challenge needs  
    │   └── recipe_name               #your choice of name  
    │       ├── aux                   #holds any auxillary files used by recipe  
    │       │   ├── some_files        #any and all auxillary files needed  
    │       └── recipes               #holds the script for this recipe  
    │           └── default.rb        #this file MUST be there  
    ├── node.json                     #this file MUST be there  
    └── values.yml                    #this file MUST be there  

        
This is a little convoluted but is necessary because our program uses chef-solo on the servers to run our automated "recipes."  You can find more info on chef and chef-solo [online](http://www.opscode.com/chef/).

Each piece is described below.

* __some_challenge_name:__ This is the folder that holds everything for your challenge. The name can be whatever you like but please dont include spaces in the name

* __values.yml:__ this file specifies the display values for the challenge on the scoreboard server. It contains yaml of the form:

        --- 
        name:        "display name"
        description: "description of challenge"
        flag:        "the_answer"

* __node.json:__ this file specifies which recipes chef-solo will run on the remote server. It contains JSON of the form

        {
            "run_list": [  "recipe[recipe1_name]", "recipe[recipe2-name]" ]
        }

Chef solo uses this run list to determine which recipe to run from the cookbooks directory.
  
    
* __cookbooks:__ a folder that holds all the recipes necessary for your challenge to be stood up and run

* __recipe-name:__ a folder that holds a recipe used by your challenge

* __aux:__ a folder that holds any files needed by your challenge. for example, the scoreboard recipe holds scoreboard.zip, an archive of the scoreboard web app

* __recipes:__ a folder that holds recipe files for this recipe. for more information on what can be placed here, check the chef website given above

* __default.rb:__ the meat of any recipe. this is the code that chef-solo will run on the server. for complete documentation on what kinds of scripts and commands you can put in this file, please visit the chef website. a sample script is given below:

        package "apache2" do
            action[:install]
        end

        bash "fake challenge" do
            user "ubuntu"
            code <<-EOH
            sudo mv $HOME/chef-solo/cookbooks/apache/aux/challenge.html /var/www/index.html
            sudo mv $HOME/chef-solo/cookbooks/apache/aux/qr-code.jpg /var/www/
            EOH
        end

        service "apache2" do
            action[:start]
        end


  As you can see, chef can use the default package installer on systems to fetch and install resources for you. Also, you can specify scripts of commands that will be run directly on the system. They do not have to use bash; other shells can be used. For a complete list, see the chef documentation. In the bash script, a user must be specified and all commands must be specified between the "code <<-EOH" and "EOH" tags.  Lastly, we see that chef can use system commands to start services for you.
  

  
Questions? Contact: ctf@mitre.org
