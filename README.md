##1 npm i koa-jwt --save
	
	module.export = { SECRET: 'mima!123@10.+gds_' } // 密钥

	app.use(koaJwt({
		secret: SECRET
	}).unless({
		// 哪些目录忽略jwt验证
		path: [
			/^/,
			/^\/users\/login/,
		]
	}))

##2 加密token
	npm i jsonwebtoken --save
	const jwt = require('jsonwebtoken')

	router.post('/login', function (ctx, next) {
		const { userName, passWord } = ctx.request.body
		const userInfo = {
			userName,
			passWord,
		}
		const token = jwt.sign(
			{
				...userInfo,
				loginTime: Date.now(),
			}, // 加密内容
			SECRET, // 密钥
			{ expiresIn: '1h' } // 过期时间
		)

		ctx.body = {
			code: 0,
			data: {
				...userInfo,
				token
			}
		}
	})

##3 解析token
	const util = require('util')
	const verify = util.promisify(jwt.verify)

	router.get('/getInfo', async (ctx, next) => {
		// 从请求头获取token
		const token = ctx.header.authorization
		try {
			// 通过util工具，将jwt.verify promise化
			const payload = await verify(
				token.split(' ')[1], // token 去掉Bearer ,取后面
				SECRET // 密钥
			)
			ctx.body = {
				code: 0,
				data: payload
			}
		} catch(e) {
			ctx.body = {
				code: 21,
				msg: 'verify error' + JSON.stringify(e)
			}
		}
	})
