# Useful hints

## To fix git: "Corrupt loose object"

find .git/objects/ -size 0 -exec rm -f {} \;
git fetch origin
git symbolic-ref HEAD refs/heads/${BRANCH_NAME}

## To disable errors when node modules are not installed, add these two lines into vscode user/workspace settings

"typescript.validate.enable": false,
"javascript.validate.enable": false,

## To remove all docker volumes

docker volume rm $(docker volume ls -qf dangling=true)
