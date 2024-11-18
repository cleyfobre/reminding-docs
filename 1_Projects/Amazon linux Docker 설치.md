#amzon #aws #ec2 #instanacetype #amazonlinux2023 #docker #dockercompose 

> [!note]
> Amazon linux 2023과 Amazon linux 2에서 Docker, Docker Compose 설치법이다. 
> 둘이 조금 다르다.

### Amazon linux 2023 에서 설치

Amazon Linux 2023 에서는 Extra를 제공하지는 않지만 Docker는 기본적으로 포함되어있다.
Amazon Linux 2 에서와 동일하게 Docker Compose Plugin 은 별도로 포함해주지 않으므로 직접 수동으로 설치해야한다. 다만, docker-compose 명령어로 별도로 사용하기 보다는 최신 버전에 맞춰 Docker [Compose 플러그인](https://docs.docker.com/compose/install/linux/#install-the-plugin-manually)으로 설치해보도록 하자. [Docker Compose Releases](https://github.com/docker/compose/releases) 사이트에서 다운로드 받은 docker-compose 를 cli-plugins 폴더 아래에 복사하면 된다.

1. Docker 설치하기

```bash
# Docker
sudo yum install -y docker
sudo service docker start
sudo usermod -a -G docker ec2-user

# Do (1) or (2)
# (1) 
exec bash

# (2)
# session 로그아웃하고 다시 로그인

docker ps
```

2. Docker Compose 플러그인 설치하기

```bash
sudo mkdir -p /usr/local/lib/docker/cli-plugins/
sudo curl -SL "https://github.com/docker/compose/releases/latest/download/docker-compose-linux-$(uname -m)" -o /usr/local/lib/docker/cli-plugins/docker-compose
sudo chmod +x /usr/local/lib/docker/cli-plugins/docker-compose

docker compose version
```

3. Compose 플러그인을 docker-compose 명령어로 사용하기

Docker Compose 플러그인을 설치했으나 기존 스크립트가 docker-compose로 되어있어서 명령어 호환성을 맞추고 싶다면 docker-compose를 심볼릭 링크 또는 alias로 등록하면 된다.

```bash
alias docker-compose='docker compose --compatibility "$@"'
docker-compose version
```


### Amazon linux 2 에서 설치

docker 설치하는 것만 다르고 docker compose는 어차피 curl로 받는 것이기 때문에 위에 amazon linux 2023 버전과 똑같다.

1. Docker 설치하기

```bash
sudo amazon-linux-extras install docker
sudo service docker start
sudo usermod -a -G docker ec2-user

# Do (1) or (2)
# (1) 
exec bash

# (2)
# session 로그아웃하고 다시 로그인

docker ps
```

2. Docker Compose 플러그인 설치와 docker-compose 명령어로 실행하는 것은 위에 있음


### alias 영원히 적용하기

alias는 설정했을 때의 세션에서만 적용되는 일시적 기능이다. 그래서 위에 docker-compose를 적용해놔도 로그아웃하면 못쓴다. 그래서 아래 처럼 적용한다.

The reason why the alias you set isn't available after logging out and logging back in is that aliases are only available in the current shell session. To make the alias persistent across logins, you need to add it to a shell configuration file that gets sourced every time you start a new shell session.

### Steps to make the alias persistent:

1. **Identify your shell**: First, check which shell you're using by running:
   ```bash
   echo $SHELL
   ```
   - If you're using Bash, the shell will be something like `/bin/bash`.
   - If you're using Zsh, the shell will be something like `/bin/zsh`.

2. **Add alias to the correct configuration file**:
   - For **Bash**, add the alias to `~/.bashrc` (for regular user sessions) or `~/.bash_profile` (if you're on a Mac).
   - For **Zsh**, add the alias to `~/.zshrc`.

3. **Edit the file**: Open the appropriate file in a text editor (e.g., `nano`, `vim`, or any editor you're comfortable with).
   ```bash
   nano ~/.bashrc
   ```
   or for Zsh:
   ```bash
   nano ~/.zshrc
   ```

4. **Add the alias**: At the bottom of the file, add the line:
   ```bash
   alias docker-compose='docker compose --compatibility "$@"'
   ```

5. **Source the file**: After saving the file, apply the changes by sourcing the file or restarting the terminal:
   ```bash
   source ~/.bashrc  # For Bash
   source ~/.zshrc   # For Zsh
   ```

Now, the alias should be available every time you start a new session or log in.