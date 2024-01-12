# PandoraNext 

> [!IMPORTANT]
> ✨ There is a new [documentation site](https://docs.pandoranext.com)，available, which provides detailed explanations on deployment, common issues, and even API calls.
>  
> ✨ Now we can [register a ChatGPT account](https://zhile.io/2023/12/09/pandoranext-introduction.html) using PandoraNext, with no restrictions and full proxy support!
> 
> ✨ [ PandoraNext Assistant GPTs](https://chat.oaifree.com/g/g-CFsXuTRfy-pandoranextzhu-shou)，are available to help with project-related questions if you have a Plus account (please refrain from attempting to extract source code).


## A Brief Introdction

* Pandora Cloud + Pandora Server + Shared Chat + BackendAPI Proxy + Chat2API = `PandoraNext`（Demo station](https://chat.oaifree.com)）
* ore powerful, but still the same ChatGPT that makes you breathe easier. Support GPTs, latest UI.
* Supports multiple login methods: (equivalent to Pandora Cloud)
  * Account Password
  * Access Token
  * Session Token
  * Refresh Token
  * Share Token
* Can have built-in tokens (all the above Tokens can be used) and supports setting passwords. (Equivalent to Pandora Server)
* Shared tokens can be configured, and there will be a sharing station with functions equivalent to [chat-shared3.zhile.io](https://chat-shared3.zhile.io) (currently 1841 regular accounts, 6 Plus).
* For full proxy mode (everything imaginable is proxied), your users only need to be able to communicate with your deployment network.
* It can be started in BackendAPI Proxy mode and directly use `Access Token` to call the interfaces of `/backend-api/` and chat2api.
* If you still have questions, join the Telegram group and let everyone watch: [@ja_netfilter_group](https://t.me/ja_netfilter_group)

<details>
<summary>
	
    Old, simple documentation. For updated and more detailed access to the documentation site.
</summary>
	
## Manual Deployment

* Download the package corresponding to the operating system and architecture in [Releases](https://github.com/pandora-next/deploy/releases).
* After unzipping, modify `config.json` in the same directory to the parameters you need.
* [Get license_id](#%E5%85%B3%E4%BA%8E-license_id) is filled in `config.json`. This is a necessary pre-step!
* Various Linux/Unix systems can be started using `./PandoraNext`.
* On Windows systems, just double-click `PandoraNext.exe`. Of course, it is best to start it in cmd.

## Docker Compose Deploy

* The warehouse already contains relevant files and directories, pull them locally, and fill in [Get license_id](#%E5%85%B3%E4%BA%8E-license_id) in `data/config.json`.
* The `data` directory contains `config.json` and `tokens.json` sample files that can be modified by yourself.
* `docker compose up -d` **Genshin Impact starts! **
 
## Docker Deploy

```bash
$ docker pull pengzhile/pandora-next
$ docker run -d --restart always --name PandoraNext --net=bridge \
    -p 8181:8181 \
    -v ./data:/data \
    -v ./sessions:/root/.cache/PandoraNext \
    pengzhile/pandora-next
```

* The container listens to the `8181` port by default and maps the host's `8181` port, which can be modified by yourself.
* You can map the directory to the `/data` directory in the container, fill in `config.json`, `tokens.json` and [Get license_id](#%E5%85%B3%E4%BA%8E-license_id) `config.json`.
* You can map the directory to the `/root/.cache/PandoraNext` directory in the container and retain the login `session` to avoid losing the login status when restarting the container.

## Nginx Configuration

```
server {
	listen 443 ssl http2;
	server_name chat.zhile.io;
	
	charset utf-8;
	
	ssl_certificate      certs/chat.zhile.io.crt;
	ssl_certificate_key  certs/chat.zhile.io.key;

	...Omit some other configuration...
	
	location / {
		proxy_http_version 	1.1;
		proxy_pass 		http://127.0.0.1:8181/;
		proxy_set_header	Connection		"";
		proxy_set_header   	Host			$http_host;
		proxy_set_header 	X-Forwarded-Proto 	$scheme;
		proxy_set_header   	X-Real-IP          	$remote_addr;
		proxy_set_header   	X-Forwarded-For    	$proxy_add_x_forwarded_for;
		
		proxy_buffering off;
		proxy_cache off;
		
		send_timeout 600;
		proxy_connect_timeout 600;
		proxy_send_timeout 600;
		proxy_read_timeout 600;
	}

	...Omit some other configuration...
}
```

* Nginx recommends enabling `http2`.
* The above configurations are only recommended configurations and can be changed according to specific circumstances.
* It is recommended to enable `ssl`, also known as `https`, otherwise browser restrictions will prevent you from copying web page content.

## config configuration

* The following is a sample `config.json` file

```json
{
  "bind": "127.0.0.1:8181",
  "tls": {
    "enabled": false,
    "cert_file": "",
    "key_file": ""
  },
  "timeout": 600,
  "proxy_url": "",
  "license_id": "",
  "public_share": false,
  "site_password": "",
  "setup_password": "",
  "server_tokens": true,
  "proxy_api_prefix": "",
  "isolated_conv_title": "*",
  "disable_signup": false,
  "auto_conv_arkose": false,
  "proxy_file_service": false,
  "custom_doh_host": "",
  "captcha": {
    "provider": "",
    "site_key": "",
    "site_secret": "",
    "site_login": false,
    "setup_login": false,
    "oai_username": false,
    "oai_password": false,
    "oai_signup": false
  },
  "whitelist": null
}
```

* `bind` specifies the binding IP and port. In docker, the IP can only use `0.0.0.0`, otherwise it cannot be mapped.
* **If you do not plan to use nginx or other reverse generation, please use `0.0.0.0` for the IP of the `bind` parameter! ! ! **
* `tls` configures PandoraNext to start directly with `https`.
    * `enabled` Whether it is enabled, `true` or `false`. Certificate and key file paths must be configured when enabled.
    * `cert_file` Certificate file path.
    * `key_file` Key file path.
* `timeout` is the request timeout, in seconds.
* `proxy_url` specifies the deployment service traffic to go through the proxy, such as: `http://127.0.0.1:8888`, `socks5://127.0.0.1:7980`
* `license_id` specifies your License Id, which can be obtained [here](#%E5%85%B3%E4%BA%8E-license_id).
* `public_share` For conversation sharing created in GPT, whether you need to log in to view it. If it is `true`, you can view it without logging in.
* `site_password` sets the password for the entire site. You need to enter this password first and make sure it is correct before you can proceed with the subsequent steps. Privacy is fully guaranteed. It must be no less than 8 digits and contain both numbers and letters!
* `setup_password` defines a setup password, which is used to call the setup interface starting with `/setup/`. If it is empty, it cannot be called. It must be no less than 8 digits and contain both numbers and letters!
* `server_tokens` sets whether to display the version number in the response header, `true` displays it, `false` does not display it.
* `proxy_api_prefix` can add a prefix to your `proxy` mode interface address, which is unexpected. Note that the characters set should be the characters allowed in the URL. Includes: `a-z` `A-Z` `0-9` `-` `_` `.` `~`
* `proxy_api_prefix` You must set a prefix that is no less than `8` and contains both `numbers` and `letters` to enable `proxy` mode!
    * `/backend-api/conversation` proxy mode ratio `1:4`
    * `/v1/chat/completions` 3.5 model scale `1:4`
    * `/v1/chat/completions` 4 model scale `1:10`, no coding required
    * `/api/auth/login` login interface ratio `1:100`, no coding required
    * `/api/auth/login2` obtains the `refresh_token` interface ratio `1:1000`, no coding required
    * `/api/arkose/token` gets `arkose_token`, ratio `1:10`
    * `/api/auth/platform/login` login platform interface ratio `1:100`, no coding required
* `isolated_conv_title` can now set the title of the isolated session, instead of the same `*` sign.
* `disable_signup` disables the account registration function, `true` or `false`.
* `auto_conv_arkose` in `proxy` mode uses the `gpt-4` model to call the `/backend-api/conversation` interface whether to automatically code, and the usage cost is `4+10`.
* `proxy_file_service` Whether to use PandoraNext's file proxy service in `proxy` mode to avoid the official file service wall.
* `custom_doh_host` configures a customized `DoH` host name. It is recommended to use the IP form. By default on startup pick the fastest one in your region among the public `DoH`s.
* `captcha` configures verification codes for some key pages.
    * `provider` verification code provider, supports: `recaptcha_v2`, `recaptcha_enterprise`, `hcaptcha`, `turnstile`, `friendly_captcha`.
    * The website parameters obtained by the `site_key` verification code provider background are information that can be published.
    * `site_secret` is a secret parameter obtained by the verification code provider's background. Do not publish it. Some vendors also call it `API Key`.
    * Whether `site_login` displays the verification code in the full-site password login interface, `true` or `false`.
    * Whether `setup_login` displays the verification code on the setup portal login interface, `true` or `false`.
    * Whether `oai_username` displays the verification code when entering the username interface, `true` or `false`.
    * Whether `oai_password` displays the verification code on the login password input interface, `true` or `false`.
* The `whitelist` mailbox array specifies which users can log in and use, username/password login is restricted, and various Token logins are restricted. Built-in tokens are unlimited.
* If `whitelist` is `null`, there will be no restriction. If it is an empty array `[]`, all accounts will be restricted. Built-in tokens will not be restricted.
* An example of `whitelist`:```"whitelist": ["mail2@test.com", "mail2@test.com"]```

## tokens configuration

* The following is a sample `tokens.json` file

```json
{
  "test-1": {
    "token": "access token / session token / refresh token",
    "shared": true,
    "show_user_info": false
  },
  "test-2": {
    "token": "access token / session token / refresh token",
    "shared": true,
    "show_user_info": true,
    "plus": true
  },
  "test2": {
    "token": "access token / session token / refresh token / share token / username & password",
    "password": "12345"
  }
}
```

* `token` supports all types written in the example file. `session token` and `refresh token` can be refreshed automatically.
* Each key is called a `token key` and can be entered as a username in the login box. As above: `test-1`, `test-2`, etc., feel free to change them.
* If `password` is set, enter the `token key` and enter the password input page to enter the match.
* If `shared` is set to `true`, this account will appear in `/shared.html`, and its link will appear on the login page
* 如果设置`shared`为`true`，则这个账号不能再在用户名登录框进行登录。
* If `shared` is set to `true`, this account can no longer be logged in in the username login box.
* The account in `/shared.html` has the same functions as the shared station. You can set your own isolation password for session isolation.
* `plus` is used to identify whether the account on `/shared.html` has golden light, and has no other function.
* `show_user_info` indicates whether to display account email information when sharing `/shared.html`. GPTs is recommended to be turned on.
* Now you can log in directly with built-in username and password. This method must set `password` and `shared` cannot be `true`.
* The format of the built-in account password is: `email, password`, which is consumed by the `0` quota.

## proxy mode interface
* Page /auth uses the account password to manually obtain `access token` and `session token`. It is just for easy access through the UI, and the consumption of `1:100` still exists.
* Page /fk uses `access token` or `session token` to obtain `share token` manually,
* Page /pk uses `share token` and manually group `pool token`.
* /backend-api/* `ChatGPT` web version interface, F12 to see the page for details.
* /public-api/* `ChatGPT` web version interface, F12 to see the page.
* /v1/* All interfaces starting with `https://api.openai.com/v1/*`, each call `1:1`.
* /dashboard/* 所有`https://api.openai.com/dashboard/*`开头的接口，每次调用`1:1`。
* **GET** /api/token/info/fk-xxx 获取share token信息，使用生成人的access token做为Authorization头，可查看各模型用量。
* **POST** /api/auth/session 通过session token获取access token，使用urlencode form传递session_token参数。
* **POST** /api/auth/refresh 通过refresh token获取access token，使用urlencode form传递refresh_token参数。
* **POST** /api/auth/login 登录获取access token，使用urlencode form传递username 和 password 参数。
* **POST** /api/auth/login2 登录获取refresh token，使用urlencode form传递username、password 和 mfa_code 参数。
* **POST** /api/token/register 生成share token
* **POST** /api/pool/update 生成更新pool token
* **POST** /v1/chat/completions 使用`ChatGPT`模拟`API`的请求接口，支持share token和pool token。
* **POST** /api/arkose/token 获取arkose_token，目前只支持`gpt-4`类型。使用urlencode form传递type=gpt-4参数。获取后可API方式调用`GPTs`
* **POST** /api/setup/reload 重载当前服务的`config.json`、`tokens.json`等配置。
* **POST** /api/auth/platform/refresh 通过`platform`的refresh token获取access token，使用urlencode form传递refresh_token参数。
* **POST** /api/auth/platform/login 登录`platform`获取access token，使用urlencode form传递username 和 password 参数。如果要获取`sess key`，增加参数`prompt=login`。
* 以上地址均需在最前面增加 `/<proxy_api_prefix>`，也就是你设置的前缀。


## 设置界面

* 必须先在`config.json`中设置`setup_password`为非空！
* 浏览器打开：`<Base URL>/setup`，其中`<Base URL>`是你部署服务的地址。

## 关于 license_id

* 在这里获取：[https://dash.pandoranext.com](https://dash.pandoranext.com)
* 复制`License Id:`后的内容，填写在`config.json`的`license_id`字段。
* 注意检查不要复制到多余的空格等不可见字符。
* 如果`config.json`中没有填写`license_id`字段，启动会报错`License ID is required`。
* **没有固定IP的情况**，IP变动后会自动尝试重新拉取。
* 更换`License Id`之后，通常需要手动删除`license.jwt`再启动。

</details>

## 其他说明

> [!CAUTION]
> * 如果你发现网页上不能复制，开启`https`或者使用`127.0.0.1`。
> * 如果你正在自定义页面元素，请保留：
>   * `Powered by PandoraNext`文字和链接。
>   * `About PandoraNext`文字和链接。
> * 如果要抹，请在**一百多个js**文件中去除若干`dom检测`代码。
> * 抹了后果如何我不说。
> * `PHP`是世界上最好的编程语言。

## 贡献者们

> 感谢所有让这个项目变得更好的贡献者们！

[![Contributors](https://contrib.rocks/image?repo=pandora-next/deploy)](https://github.com/pandora-next/deploy/graphs/contributors)

## Star历史

![Star History Chart](https://api.star-history.com/svg?repos=pandora-next/deploy&type=Date)
