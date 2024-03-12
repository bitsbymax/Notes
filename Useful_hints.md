# Useful_hints

- To fix git: "Corrupt loose object"
find .git/objects/ -size 0 -exec rm -f {} \;
git fetch origin
git symbolic-ref HEAD refs/heads/${BRANCH_NAME}
