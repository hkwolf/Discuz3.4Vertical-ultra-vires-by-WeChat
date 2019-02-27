# Discuz3.4-Vertical-ultra-vires-by-WeChat
DZ3.4版本的微信登录漏洞进行垂直越权
最近看到一个Discuz逻辑漏洞，该漏洞源于Discuz微信登陆功能。
Discuz 3.4默认安装了微信登陆，插件默认是不启用的。
利用这个漏洞攻击者可以越权登陆论坛其他会员的账号甚至是管理员账号，主要看用户是否激活了微信登录的功能。

文件：/upload/source/plugin/wechat/wechat.inc.php
代码：
if($_G['wechat']['setting']['wechat_qrtype']) {
			$mpmember = C::t('#wechat#common_member_wechatmp')->fetch_by_openid($wxopenid ? $wxopenid : $_GET['wxopenid']);
			$mpmembers = C::t('common_member')->fetch_all(array_keys($mpmember));
			if ($mpmembers) {
				$memberfirst = array_shift($mpmembers);
				$member = getuserbyuid($memberfirst['uid'], 1);
				if($member) {
					setloginstatus($member, 1296000);
					$url = wsq::wxuserregisterUrl($memberfirst['uid']);
					if ($ac == 'wxregister') {
						dheader('location: ' . $url);
					} else {
						showmessage('wechat:wechat_member_register_succeed', $url);
					}
				}
			}
		}
    
    
越权操作：   
1、如果admin激活了微信的插件，但是没有去绑定微信，会将admin写入到common_member_wechatmp表里面去，这时候就可以对admin进行越权操作；
2、该漏洞还可以针对指定uid绑定的用户进行越权操作；
3、登录第一个openid为空的用户，也是可以进行越权的；
