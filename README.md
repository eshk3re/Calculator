Создаём docker-compose.yml для запуска контейнеров с gitlab и gitlab-runner:  
`  
version: '2.6'  
services:  
  gitlab:  
    image: gitlab/gitlab-ce:16.10.0-ce.0  
    container_name: gitlab  
    restart: always  
    hostname: 'gitlab.example.com'  
    environment:  
      GITLAB_OMNIBUS_CONFIG: |  
        external_url 'https://gitlab.example.com'  
    ports:  
      - '80:80'
      - '443:443'
      - '22:22'
    volumes:
      - '/srv/gitlab/config:/etc/gitlab'
      - '/srv/gitlab/logs:/var/log/gitlab'
      - '/srv/gitlab/data:/var/opt/gitlab'
    shm_size: '256m'
    networks:
      - gitlab_net

  gitlab-runner:
    image: gitlab/gitlab-runner:alpine
    container_name: gitlab-runner
    restart: unless-stopped
    depends_on:
      - gitlab
    volumes:
      - /srv/gitlab-runner:/etc/gitlab-runner
      - /var/run/docker.sock:/var/run/docker.sock
    networks:
      - gitlab_net

networks:
  gitlab_net:
    driver: bridge
`
