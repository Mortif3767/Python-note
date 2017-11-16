# django, flask非对称多对多数据库关系  
  
非对称数据库关系，多见于模型自引用建立的类似关注者、被关注者的多对多关系，两个博客项目写下来，把写法记录下来供以后参考。 
   
### django写法  
```
from django.db import models
from django.contrib.auth.models import User

class Contact(models.Model):
    follower = models.ForeignKey(User, related_name='followed_set')   #followed
    followed = models.ForeignKey(User, related_name='followers_set')  #followers
    created = models.DateTimeField(auto_now_add=True, db_index=True)

    class Meta: 
        ordering = ('-created',)

    def __str__(self): 
        return '{} follows {}'.format(self.follower, self.followed) 

User.add_to_class('followed', models.ManyToManyField('self', 
                                                     through=Contact, 
                                                     related_name='followers', 
                                                     symmetrical=False))
```
在这里使用django内置的User模型，使用add_to_class方法添加多对多字段，Contact为中间表。  
Contact在这里相当于对User建立了两个多对一关系：  
- 第一个字段follower关注者，对应User中的字段followed被关注者，user1.followed意为用户1关注的人，Contact里的follower指的是user1。  
- 第二个字段followed被关注者，对应User中的字段followers关注者，user1.followers意为关注用户1的人，Contact里的followed指的是user1。  

**创建关注关系**  
```
user1 = User.objects.get(id=1) 
user2 = User.objects.get(id=2) 
Contact.objects.get_or_create(follower=user1, followed=user2)
```
### flask写法  
```
class Contact(db.Model):
	__tablename__ = 'follows'
	follower_id = db.Column(db.Integer, db.ForeignKey('users.id'), primary_key = True)
	followed_id = db.Column(db.Integer, db.ForeignKey('users.id'), primary_key = True)
	created = db.Column(db.DateTime, default=datetime.utcnow)

class User(UserMixin, db.Model):
	__tablename__='users'
	id = db.Column(db.Integer,primary_key=True)
    # ...
	followed = db.relationship('Follow',
		                   foreign_keys=[Follow.follower_id],
		                   backref=db.backref('follower', lazy='joined'),
		                   lazy='dynamic',
		                   cascade='all, delete-orphan')
	followers = db.relationship('Follow',
		                    foreign_keys=[Follow.followed_id],
		                    backref=db.backref('followed', lazy='joined'),
		                    lazy='dynamic',
		                    cascade='all, delete-orphan')
```
Contact为中间表，User表中分别定义followed和followers两个一对多关系，原理同django类似。  
**创建关注关系**  
```
c = Contact(follower=user1, followed=user2)
db.session.add(c)
db.session.commit()
```
