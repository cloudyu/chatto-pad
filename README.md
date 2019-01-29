# chatto-pad
A simple ctf pad based on codimd and rocket.chat. To make communication easier!

# 构成
在线编辑器 [codimd](https://github.com/hackmdio/codimd)
协作聊天 [rocket.chat](https://rocket.chat/)
前端 [chatto-front](https://github.com/cloudyu/chatto-front)
后端 [chatto-bakend](https://github.com/cloudyu/chatto-backend)


# 网络
![](https://i.imgur.com/CxIvwFV.png)

# 配置
## codimd
修改 docker-compose.yml中为 backend 的地址
```
      - CMD_OAUTH2_TOKEN_URL=http://chatto:8000/codimd/token
      - CMD_OAUTH2_AUTHORIZATION_URL=https://chatto.cloudyu.me/codimd/auth
      # 上面这行需要修改 !!!!
      - CMD_OAUTH2_USER_PROFILE_URL=http://chatto:8000/codimd/profile
```
如果内网连通. `CMD_OAUTH2_TOKEN_URL` 和 `CMD_OAUTH2_USER_PROFILE_URL` 可以填内网地址. 
`CMD_OAUTH2_AUTHORIZATION_URL` 一定要填外网的

## rocket.chat
docker-compose up 手工配置
需要注册管理员账号 进行如下配置
### 添加管理者权限
`/admin/permissions`
1. 新建一个 `manager` 角色, 并授予给期望拥有建立pad权限的人
2. 新建一个 `bot` 角色, 点上`add-user-to-joined-room`, `archive-room`, `create-p`, `set-owner`, `unarchive-room`, `view-full-other-user-info`
`/admin/users`
1. 创建一个bot 用户 授予 bot 角色. 密码建议随机化!
`/admin/oauth-apps`
1. 添加一个应用, 类似下面的配置
![](https://i.imgur.com/xFBOIqR.png)
`admin/General`
1. 设置 iframe 集成
![](https://i.imgur.com/KUj9aGI.png)

## chatto backend
配置 settings.py

```
CHATTO_PAD = {
    'OAUTH': {
        'SERVER_URL_EXT': 'https://chat.cloudyu.me',   # 外站
        'SERVER_URL_INT': 'http://rocketchat:3000',      # 内部
        "CALLBACK_URL": 'https://chatto.cloudyu.me/%23/callback',
        'CLIENT_ID': 'CLIENT_ID',
        'CLIENT_SECRET': 'CLIENT_SECRETCLIENT_SECRET',
    },
    'ROCKET_CHAT': {
        'MANAGER_ROLE': 'manager',                  # 可以添加pad 权限
    },
    'CODIMD': {
        'SERVER_URL_EXT': 'https://codimd.cloudyu.me',  # 外部链接
        'SERVER_URL_INT': 'http://codimd:3000',  		# 内网链接
    }, 
    'ROCKET': RocketChat('chatto-bot', 'password', 'http://rocketchat:3000')
}
```
OAUTH 中的 CLIENT 需要管理员在 rocket.chat 中申请. 回调注意要统一
MANAGER_ROLE 需要在 手动建立这个名字的权限. 并授予给用户.
    如果填写 'user' 代表任何人都可以新建 pad


## chatto front
配置 src/Config.vue
```
const codimd = 'https://codimd.cloudyu.me/'
const chat = 'https://chat.cloudyu.me/group/'
const apiServer = 'https://chatto.cloudyu.me/'
const wsServer = 'wss://chatto.cloudyu.me/ws/'
```
全部地址都需要外网可以访问

## nginx
自行配置好ssl证书. 域名

# build
配置好后 各种docker-compose up -d 就ok
