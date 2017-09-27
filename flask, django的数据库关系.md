# flask, django的数据库关系  
  
在学完flask框架，开始学习django时，关于数据库“多对一”，“多对多”关系的定义有一些不同，总结一下笔记，以便以后查阅。  
  
## 多对一关系  
  
**flask写法**，示例模型为：用户权限`Role`为“一”的一方，用户`User`为“多”的一方。  
```
class Role(db.Model):
    # ...
    users = db.relationship('User', backref='role', lazy='dynamic')
    
class User(db.Model):
    # ...
    role_id = db.Column(db.Integer, db.ForeignKey('roles.id'))
```
  
**django写法**  
```
class Role(models.Model):
    # ...
    
class User(models.Model):
    role = models.ForeignKey(Role, related_name=users)
```
