202403271550
Status: #idea
Tags: 

# git

Update existing remotes:
```bash
git remote set-url origin $(git remote get-url --push origin | sed -e 's/GreenseaSystems/gsiq-bayonet/'); git pull origin master; git remote -v
```

# References

