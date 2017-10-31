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
