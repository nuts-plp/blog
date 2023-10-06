# JWT JSON-WEB-TOKEN鉴权

[go-jwt](https://github.com/go-ini/ini)

#### JWT 主要用于登录鉴权验证

示例

```go
package token

import (
	"fmt"
	"time"

	"github.com/golang-jwt/jwt"
)

type MyClaim struct {
	UserId    int64
	UserEmail string
	*jwt.StandardClaims
}

// GenerateToken
// @Summary 生成token
// @Description 通过传入用户id 用户邮箱 token签发人 密文 token有效时间 生成token字符串和错误信息
// @param id int64, email issuer secret string, expire time.Duration
// @Return tokenS ,err
func GenerateToken(id int64, email, issuer, secret string, expire time.Duration) (string, error) {

	//自定义声明，把自己要签名的内容放进声明里
	clime := MyClaim{
		id,
		email,
		&jwt.StandardClaims{
			ExpiresAt: time.Now().Add(expire).Unix(),
			Issuer:    issuer,
			IssuedAt:  time.Now().Unix(),
		},
	}
	// 选择算法对声明进行加密生成token
	token := jwt.NewWithClaims(jwt.SigningMethodHS256, clime)
	// 对token进行签名
	return token.SignedString([]byte(secret))
}

// ParseToken
// @Summary 解析token
// @Description 通过传入密文和token字符串获取用户的声明信息
// @Param tokenS secret
// @Return bool *MyClaim
func ParseToken(tokenS, secret string) (bool, *MyClaim) {
	clain := new(MyClaim)
	// 解析token字符串 获取用户的声明信息
	token, err := jwt.ParseWithClaims(tokenS, clain, func(token *jwt.Token) (interface{}, error) {
		return []byte(secret), nil
	})
	if err != nil {
		fmt.Println("解析失败！")
		return false, nil
	}
	if !token.Valid {
		return false, nil
	}
	return true, clain

}

func main() {
	generateToken, err := token.GenerateToken(id, email, issuer, secret, expire)
	if err != nil {
		return
	}
	fmt.Println(generateToken)
	ok, claim := token.ParseToken(generateToken, secret)
	if !ok {
		return
	}
	fmt.Println(claim)

}

```



