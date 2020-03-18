# 练习题
### （1）  
     yanxuan库test表结构：  
     int id  
     int Date  
     int GuestId    
#### 语句：   
     SELECT Date ,count(Date) from Test group by Date;     
####  结果集：  
     Date count  
      13    2  
      14    4  
      15    3  
####  结论：    
      group by date以后，得到的count(Date)是每个Date内的数目
####  语句：求不同Date个数  
      参考以下做法：  
     （没有重名的情况下：select count(distinct 姓名) from table      
      有重名的情况（但是重名的人年龄不一样）：select count(distinct(姓名,年龄)) from table）   
      select  count(distinct date) from Test; 
####  语句：求date=14的用户黏度   ：15日和14日都参加的guests/14日参加的guests    
      select (SELECT count(*) from Test as a inner join Test as b on a.GuestId=b.GuestId where a.Date=14 and b.Date=15) /             (SELECT count(*) from Test where date=14);     
      
      
      
      
      
   
     

     
