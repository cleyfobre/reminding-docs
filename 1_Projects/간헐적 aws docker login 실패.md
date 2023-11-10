#aws #docker #jenkinsfile #jenkins #pipeline 

#todo
```sh
**15:21:36**  ------------------------- deploy start -------------------------
**15:21:36**  /home/ubuntu/backend/deploy
**15:21:36**  green up
**15:21:36**   yangcheon-road-api Pulling 

**15:21:37**   5843afab3874 Already exists 
**15:21:37**   53c9466125e4 Already exists 
**15:21:37**   d8d715783b80 Already exists 
**15:21:37**   9bb07a182b60 Pulling fs layer 
**15:21:37**   4b85b3daeb68 Pulling fs layer 
**15:21:37**   05ba8551133c Pulling fs layer 
**15:21:37**   47e224028525 Pulling fs layer 
**15:21:37**   47e224028525 Waiting 
**15:21:37**   4b85b3daeb68 Downloading [==============================================>    ]  3.523kB/3.751kB
...
```

```sh
**15:17:53**  ------------------------- deploy start -------------------------
**15:17:53**  /home/ubuntu/backend/deploy
**15:17:53**  green up
**15:17:53**   yangcheon-road-api Pulling 
**15:17:53**   yangcheon-road-api Error 
**15:17:53**  Error response from daemon: pull access denied for 087124035214.dkr.ecr.ap-northeast-2.amazonaws.com/yangcheon-road-api, repository does not exist or may require 'docker login': denied: Your authorization token has expired. Reauthenticate and try again.
**15:17:55**  WARNING! Your password will be stored unencrypted in /home/ubuntu/.docker/config.json.
**15:17:55**  Configure a credential helper to remove this warning. See
**15:17:55**  [https://docs.docker.com/engine/reference/commandline/login/#credentials-store](https://docs.docker.com/engine/reference/commandline/login/#credentials-store)
...
```
