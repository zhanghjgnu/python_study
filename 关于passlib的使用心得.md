关于passlib的使用心得
    用python做个密码验证功能，重复运行下面这个脚本会给我不同的哈希输出，即使我没有使用salt并且执行相同的算法

from passlib.context import CryptContext

myctx = CryptContext(schemes=["sha256_crypt", "md5_crypt"])
print( myctx.default_scheme())
hash1 = myctx.hash("eastwill")

print("eastwill="+hash1)

print(myctx.verify("eastwill",hash1))
print(myctx.verify("eastwill8",hash1))

sha256_crypt
eastwill=$5$rounds=535000$7bCRQSzMbSGeno0b$Tl1Y9d4BC9gMJkV9tMHbRg/XLpByGLlZcrDTZUFQx54
True
False

    查资料可知这是因为passlib对于大多数哈希类型，当调用^{}时，passlib会自动为密码加盐：
classmethod PasswordHash.encrypt(secret, **kwds)
Digest password using format-specific algorithm, returning resulting hash string.
For most hashes supported by Passlib, the returned string will contain: an algorithm identifier, a cost parameter, the salt string, and finally the password digest itself.

    所以对明文做hash和数据库中存放的加密密码做对比的老办法不好用了，passlib要通过vertify来验证明文密码是否正确，即
myctx.verify("eastwill",hash1)

    这样数据库中可以存储明文密码某次hash后的值，以后和新的用户提交明文密码hash后做vertify,其实encrypt()函数就是hash()函数的别名

参考文档:https://passlib.readthedocs.io/en/stable/lib/passlib.context.html
