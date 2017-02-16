# Import Remote DB for Roots based WordPress

## Summary

This is a collection of shell scripts to help automate the retrieval and importing of content from a remote server into a local [WordPress roots environment](https://roots.io/). Certain scripts could be used outside of a roots setup (like `get_db`), while others are more tailored to a roots folder structure.


## Setup Overview

* Clone this repo to the correct place within a roots folder structure
* Create an ssh Host configuration in `~/.ssh/config`
* Update relevant values in `get_db` and `import_to_local`
* Ensure that Vagrant is running
* Get the remote db and then import it into the guest machine


## Details

### Clone this Repo

I recommend structuring your project as outlined in the [Installation instruction of the Trellis Docs](https://roots.io/trellis/docs/installing-trellis/), so something like:

```
example.com/      # → Root folder for the project
├── trellis/      # → Trellis files
├── db/           # → This repo, to be created when cloning
└── site/         # → A Bedrock-based WordPress site
    └── web/
        ├── app/  # → WordPress content directory (themes, plugins, etc.)
        └── wp/   # → WordPress core (don't touch!)
```

To add this repo to the above setup, change to the root directory and clone this repo to the `db` folder.

```
cd path/to/example.com
git clone https://github.com/bgturner/roots_db_scripts db
```

### Create a SSH Host Configuration

Using ssh to connect to remote machines makes life much easier. Part of that involves creating a SSH Host Configuration. This is basically a way to alias the ssh connection to an easy to remember string. I typically use a client code as the string. So using the above `example.com` host as our project, I might use an alias of `example` or even `ex`. The point is that it is simple to remember, and unique. For this readme, let's just use `example_alias`.

SSH Host Configurations are awesome because they allow us to change a complex command like `ssh -i ~/.ssh/id_rsa web@example.com` to `ssh example_alias`.

To do this, we add a Host Configuration to `~/.ssh/config`. If this file does not exist, create it. If it already exists, append this to the other entries. Using the above ssh command as an example, add a configuration for the `example.com` remote host:

```
Host example_alias
  # Your choice if you want to reference by domain, or IP
  # HostName xxx.xxx.xxx.xxx
  HostName example.com
  # Point to the private key used to access this host
	IdentityFile ~/.ssh/id_rsa
	user web
```

We should be able to access the remote host now by issuing `ssh example_alias`.

Note that the user `web` is the default Trellis, non-root user. If you changed that value in `trellis/group_vars/all/users.yml` then adjust the above config accordingly.

If you want to read more about SSH Host Configuration, I think that [Digital Ocean has a great article about this](https://www.digitalocean.com/community/tutorials/how-to-configure-custom-connection-options-for-your-ssh-client).

### Update relevant values within scripts

Make sure to update values within the scripts for your specific setup. At the very least update the `ssh_alias` value in `get_db` to the host alias created above, in this case `example_alias`. Review the `import_to_local` script as well since it has search and replace commands it executes.

### Ensure Vagrant is Running

Make sure the VM is running:

```
cd ../trellis
vagrant up
```

### Get the DB and Import It

This is just chaining the two scripts:

```
cd ../db
./get_db && ./import_to_local
```

