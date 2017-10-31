建立一个简易Model  
```
class Person(models.Model):
    GENDER_CHOICES=(
        (1,'Male'),
        (2,'Female'),
        )
    name=models.CharField(max_length=30,unique=True,verbose_name='姓 名')   
    birthday=models.DateField(blank=True,null=True)
    gender=models.IntegerField(choices=GENDER_CHOICES)
    account=models.IntegerField(default=0)
```  
### blank  
设置为True时，字段可以为空。设置为False时，字段是必须填写的。字符型字段CharField和TextField是用空字符串来存储空值的。  
  
### null  
设置为True时，django用Null来存储空值。日期型、时间型和数字型字段不接受空字符串。所以设置IntegerField，DateTimeField型字段可以为空时，需要将blank，null均设为True。  
如果想设置BooleanField为空时可以选用NullBooleanField型字段。  
  
### max_length  
为CharField型字段设置最大长度。  
  
### choices  
由元素为2-tuples的序列(list或者tuple)作为字段的choices。2-tuple的第一个元素存储在数据库中，第二个元素可由get_FOO_display方法得到。  
```
>>>p=Person(name='Sam',gender=1)
>>>p.save()
>>>p.gender
1
>>>p.get_gender_display()
u'Male'  
```  
如果choices的选项过多的话，最好考虑使用ForiegnKey。  
  
### default  
为字段设定默认值。  
默认值不能是一个可变对象（模型实例，列表，集合等），作为到同一个实例的参考，该对象将用作所有新的模型实例中的默认值。相反，在一个可调用的对象中封装所需的默认值。例如，如果你有一个自定义JSONField，并希望指定一个作为默认的字典，使用一个lambda表达式如下：  
```
contact_info = JSONField("ContactInfo", default=lambda:{"email": "to1@example.com"})
```  
  
### verbose_name  
设置此字段在admin界面上的显示名称。  
  
### unique  
设置为True，此字段在数据库中必须是唯一的。  
```
>>>p=Person(name='Sam',gender=1)
>>>p.save()
>>>p=Person(name='Sam',gender=2)
>>>p.save()
IntergrityError: column name is not unique
```  
  
### primary_key  
如果设置为True，则此字段成为Model的主键。一般情况下，django会为Model自动添加一个叫id的IntegerField字段作为主键。  
