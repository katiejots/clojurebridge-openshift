# ClojureBridge OpenShift

This repository contains the [ClojureBridge](http://www.clojurebridge.org) Global Growth web application, as described in the [curriculum for the Brisbane course](https://github.com/ClojureBridgeBrisbane/curriculum). A few small changes have been made, as explained below, to make the app run with [this OpenShift Clojure cartridge](https://github.com/openshift-cartridges/clojure-cartridge).

To get this app running on OpenShift, [sign up for OpenShift Online](https://openshift.redhat.com/app/account/new), [install the RHC command line tools](https://www.openshift.com/developers/rhc-client-tools-install), and run the following commands:

    rhc setup
    rhc app create globalgrowth http://cartreflect-claytondev.rhcloud.com/github/openshift-cartridges/clojure-cartridge --from-code=https://github.com/codemiller/clojurebridge-openshift.git -s

The `-s` makes the OpenShift application auto-scalable; this is optional.

# Steps Required to Recreate

If you have created the Global Growth web app following the ClojureBridge instructions and wish to tweak your version to run it on OpenShift, you can achieve that by doing the following:

. Install SSH, Git and Ruby on your machine, if you don't already have them.

. Install the [RHC command line tools](https://www.openshift.com/developers/rhc-client-tools-install), which usually just means running the command `gem install rhc`.

. Run the command `rhc setup`.

. Change into the source directory for your Global Growth application (`cd global-growth`).

. If you haven't yet made it a Git repository, run the command `git init`.

. Run the following command to create an OpenShift Clojure app called _globalgrowth_:

    rhc app create globalgrowth http://cartreflect-claytondev.rhcloud.com/github/openshift-cartridges/clojure-cartridge -s --no-git

Once it has finished running, you should see a line in the output containing a URL like this:

    ssh://0123456789abcdef01234567@globalgrowth-yourdomain.rhcloud.com/~/git/globalgrowth.git/
    
Copy the SSH URL and use it in the following command.

. Run the following command, replacing the SSH URL with the one for your app:

    git remote add openshift ssh://0123456789abcdef01234567@clojureapp-yourdomain.rhcloud.com/~/git/clojureapp.git/
    
. There are three changes to make to the code to run the app on OpenShift. Firstly, open _project.clj_ and change the value for _:main_ from _global-growth.core_ to _global-growth.web_.

. Next, open *src/global_growth/web.clj*. At the top of the file, add a require statement for Jetty like this:

    [ring.adapter.jetty :as jetty]

. Finally, at the very bottom of _web.clj_, add the following code:

    (defn -main
      [& args]
      (jetty/run-jetty handler {:port (Integer/parseInt (get (System/getenv) "OPENSHIFT_CLOJURE_HTTP_PORT" "3000"))
                                :host (get (System/getenv) "OPENSHIFT_CLOJURE_HTTP_IP" "0.0.0.0")}))

This tells the app what port and host to use when it is running on OpenShift.

. Add, commit and push your changes to OpenShift with the following commands:

    git add .
    git commit -m "Added required changes for code to run on OpenShift"
    git push -f openshift master





