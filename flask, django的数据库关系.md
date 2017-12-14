# flask, django的数据库关系  
  
在学完flask框架，开始学习django时，关于数据库“多对一”，“多对多”关系的定义有一些不同，总结一下笔记，以便以后查阅。  
  
## 多对一关系  
  
**flask写法**，示例模型为：用户权限`Role`为“一”的一方，用户`User`为“多”的一方。  
```
class Role(db.Model):
    __tablename__ = 'roles'
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
需要注意的区别：  
1.django的数据库关系全部定义在多的一方，有一向多的反向引用定义在`related_name`里，该参数如果不定义，则django默认为多方表名+“_set”，本例即为`user_set`。  
2.flask的数据库关系，在多方定义外键，在一方定义关系，反向引用定义在一方的backref里。  
  
## 多对多关系  
  
**flask**写多对多关系比较复杂，必须写关系表，示例模型为：学生(Student)与课程(classes)。  
```
registrations = db.Table('registrations',
    db.Column('student_id', db.Integer, db.ForeignKey('students.id')),
    db.Column('class_id', db.Integer, db.ForeignKey('classes.id'))
)

class Student(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    # ...
    classes = db.relationship('Class', secondary=registrations,
                              backref=db.backref('students', lazy='dynamic'),
                              lazy='dynamic')
                              
class Class(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    #...
```
而**django**的写法就很简单：  
```
class Student(models.Model):
    classes = models.ManyToManyField(Class, related_name='students')
    
class Class(models.Model):
    # ...
```
可见django要简便智能的多，但是flask的书写方式会让你更清楚多对多关系表的工作原理。  
在这两个例子中，可以用`student.classes.all()`和`class.students.all()`相互引用。  

需要注意的是，django的表中，related_name并非必须，如果未设置将会用**表名+“_set”**替代。  

## django的聚合

主要内容可见：http://python.usyiyi.cn/translate/django_182/topics/db/aggregation.html  

这里主要提醒一下，在反向聚合中，如果没有定义`related_name`，可以直接使用表名的小写作引用。  
例如：  
```
class Publisher(models.Model):
    name = models.CharField(max_length=300)
    num_awards = models.IntegerField()

class Book(models.Model):
    publisher = models.ForeignKey(Publisher)
    pubdate = models.DateField()
    
p = Publisher.objects.annotate(Count('book'))  #注意book的使用
c = Publisher.objects.aggregate(oldest_pubdate=Min('book__pubdate'))
```
