# Adding flags to prompt

Managing multiple Kubernetes environments can be a bit tricky to juggle, especially if you want to make sure you don't deploy anything to production by mistake, or that you're looking on stage env logs to debug a problem that does not exist in prod (yet). It would be better to have some kind of notification what configuration is currently used by kubectl command. This is when I decided to create a flag in my terminal prompt.

### Editing prompt

Editing prompt is really simple, as you just need to mangle `PS1` environmental variable. This is one of the first things I do when I start working on a new machine (or OS) is shorten it - I just hate to see the whole path (although it could occasionally save some time). My default prompt looks like this:

    # ~/.bash_profile
    export PS1="\[\033[0;92m\](\W) \[\e[m\]$ "

It starts with a color definition (`\[\033[0;92m\]`) which represents light green, then there is a wildcard (`(\W)`) which means that we'll have the name of the current directory in the brackets. After that, I reset the color to white (`\[\e[m\]`), add a dollar sign. As you can see, editing is quite simple, just grab some cheat sheet with color and wildcards and you're ready to go.

### Custom flag

If you look closely, the prompt is nothing more than just a string with some variables injected. Even text formatting is applied to some special set of characters, which means that we can add another part to the prompt that will be formatted as well. This is what we can use to add some flag that can notify us that we are about to break something in production.

At my current company we're using two alias scripts to switch between those environments:

    # ~/.bash_profile
    alias kiprod='kubectl config use-context some-production-stuff'
    alias kistage='kubectl config use-context some-stage-stuff'

Whenever we use one of the scripts, two things happen. First, we are instantly notified that context is changed. Then we can verify which one is used by looking at `kubectl config get-contexts` command output:

    (some-dir) $ kistage
    Switched to context "some-stage-stuff".
    (some-dir) $ kubectl config get-contexts
    CURRENT   NAME                    CLUSTER                 AUTHINFO               NAMESPACE
    *         some-stage-stuff        some-stage-stuff        some-stage-stuff        
              some-production-stuff   some-production-stuff   some-production-stuff   
    
    (some-dir) $ kiprod
    Switched to context "some-production-stuff".
    (some-dir) $ kubectl config get-contexts
    CURRENT   NAME                    CLUSTER                 AUTHINFO               NAMESPACE
    *         some-production-stuff   some-production-stuff   some-production-stuff   
              some-stage-stuff        some-stage-stuff        some-stage-stuff        

In this case, we can easily check what context is used by grepping the output and looking if the line with _production_ context also has an asterisk marking it as being currently used:

    (some-dir) $ kistage
    Switched to context "gke_ingrid-head_europe-west1-b_stage-v1".
    (some-dir) $ kubectl config get-contexts | grep "*" | grep production | wc -l
    0

    (some-dir) $ kiprod
    Switched to context "gke_ingrid-head_europe-west1-b_production-v1".
    (some-dir) $ kubectl config get-contexts | grep "*" | grep production | wc -l
    1

Knowing this, we can create a function in our `~/.bash_profile` to get an appropriate text (later to be added to the prompt):

    # ~/.bash_profile
    function get_kiprod(){
        kip=`kubectl config get-contexts | grep "*" | grep production | wc -l`
        if [ "$kip" -eq "1" ]; then
                echo "[prod]"
        fi
    }

**Note** that for non-production context our function does not print anything (as we only want to be bothered it _production_ is used).

Finally we can add our function to the prompt, best with some colour formatting. I chose red text (`\[\e[31m\]`), because it's naturally alarming in my opinion:

    # ~/.bash_profile
    export PS1="\[\033[0;92m\](\W)\[\e[31m\]\$(get_kiprod)\[\e[m\] $ "

(In order to execute our function we need to add `\$(get_kiprod)`, which in the end will be replaced by the function output any time the prompt is being rendered):

    (some-dir) $ kistage
    Switched to context "gke_ingrid-head_europe-west1-b_stage-v1".
    (some-dir) $ kiprod
    Switched to context "gke_ingrid-head_europe-west1-b_production-v1".
    (some-dir) [prod] $ 

It works! I highly recommend creating such flag for anything that should notify you and possibly prevent you from making some mess (especially in the production environment).
