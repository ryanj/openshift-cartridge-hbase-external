# hbase - external service cartridge

This OpenShift add-on cartridge is meant to be modified and reused by OpenShift operators in order to provide configuration settings for applications that rely on hbase data services.

Installing your modified hbase cart in an [OpenShift Enterprise](https://www.openshift.com/products/enterprise) or [OpenShift Origin](https://www.openshift.com/products/origin) deployment can help developers autoconfigure their hosted applications to take advantage of your existing hbase datastores (outside of OpenShift).

## Clone and Modify this cart
First, make a local clone of this repo, and push another clone of the source to a private git repo (hosted inside your firewall).  If you include your hbase configuration strings in your modified cartridge source, then make sure to keep that source somewhere private.  A privately hosted git repo is ideal here, since it can be accessed by your OpenShift deployment.  You could also try modifying the `bin/setup` script in order to pull configurations strings from another location on your private network.

Modify the files in `env` so that they include your hbase configuration strings.  Then, update `bin/setup`, and `bin/install` to match your changes.

Finally, update the Env var documentation in `metadata/manifest.yml` before adding and committing your changes.  Push your resulting modifications to your private git hosting solution (not to a public git repo, like GitHub).

## Test your changes
You can test your modified cartridge before adding it to the list of available cartridge services.

Try creating the following application using the `rhc` command line tool:

    rhc app create hbasetodo python-2.7 http://yourgitserver.com/username/my-hbase-cart.git --from-code=https://github.com/ryanj/flask-hbase-todos

You can verify that your configuration strings were successfully published into the system environment by connecting to your application using `ssh`:

    rhc ssh hbasetodo
    env | grep HBASE

## Cartridge Installation
To add your modified cart to your global OpenShift cartridge list, run the following as an OpenShift administrator:

    oo-admin-ctl-cartridge -c import --url https://yourhostedgit.com/username/my-hbase-cart/master/metadata/manifest.yml --activate

This should allow users of your OpenShift deployment to see your external hbase solution as an available configuration option in the system cartridge listing:

    rhc cartridge list

It should also allow them to request your hbase application configuration strings as a formal cartridge dependency:

    rhc app create hbasetodo python-2.7 hbase-1.0 --from-code=https://github.com/ryanj/flask-hbase-todos

OpenShift includes additional tools for helping operators remove, unpublish, or upgrade their OpenShift cartridges:

    oo-admin-ctl-cartridge -c delete --ids hbase-1.0
