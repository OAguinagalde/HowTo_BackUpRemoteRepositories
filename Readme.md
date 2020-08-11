# HowTo_BackUpRemoteRepositories

How to create a `bare mirror clone` of a repository and update it periodically for back up purposes.

From [docs.github.com](https://docs.github.com/en/github/creating-cloning-and-archiving-repositories/duplicating-a-repository):

----

## Mirroring a repository in another location

If you want to mirror a repository in another location, including getting updates from the original, you can clone a mirror and periodically push the changes.

1. Open Git Bash.
2. Create a bare mirrored clone of the repository.

    ```ps1
    git clone --mirror https://github.com/exampleuser/repository-to-mirror.git
    ```

3. Set the push location to your mirror.

    ```ps1
    cd repository-to-mirror.git
    git remote set-url --push origin https://github.com/exampleuser/mirrored
    ```

As with a bare clone, a mirrored clone includes all remote branches and tags, but all local references will be overwritten each time you fetch, so it will always be the same as the original repository. Setting the URL for pushes simplifies pushing to your mirror. To update your mirror, fetch updates and push.

```ps1
git fetch -p origin
git push --mirror
```

----

After generating those mirrors you can generate a very simple powershell script to run everyday and fetching the original remote.

```ps1
# The action that will be executed regularly: fetch the origin repo
$action = (New-ScheduledTaskAction -Execute 'Powershell.exe' -Argument '-NoProfile -WindowStyle Hidden -command "& {

    # The powershell script code that the task will run in each execution
    # For testing, you can run "notepad.exe" as the command
    cd C:\my\mirror\folder
    git fetch -p origin
    git push --mirror

}"')

# Trigger the task 2 seconds from now, once, if you want to test it.
# Or every day at 11:00 AM ->
# New-ScheduledTaskTrigger -Daily -At 11am
$trigger = (New-ScheduledTaskTrigger -At ((get-date) + (new-timespan -seconds 2)) -Once)

# Register the Task for it to happen.
# Tasks are stored as xml files in C:\Windows\System32\Tasks
Register-ScheduledTask -Action $action -Trigger $trigger -TaskName "NAMEOFTASK" -RunLevel Highest
```
