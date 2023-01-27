# Git Repository

https://github.com/thoughtworks/talisman#linuxunix

## Installation to a single project

# Download the talisman installer script

curl https://thoughtworks.github.io/talisman/install.sh > ~/install-talisman.sh
chmod +x ~/install-talisman.sh

# Install to a single project

cd my-git-project

# as a pre-push hook

~/install-talisman.sh

# or as a pre-commit hook

~/install-talisman.sh pre-commit

# To ignore files in talisman scan

create a file .talismanrc and add the file name and checksum to ignore
