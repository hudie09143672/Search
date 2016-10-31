##查询用户test1可以查看的页面（Sys_menu）：
    伪代码：
    1.在cf_role中查找LoginName为test1的用户的UserID
    2.在cf_userrole中查找test1的角色的RoleID
    3.cf_privilege与sys_menu进行左连接
    4.在cf_privilege中遍历， PrivilegeAccess='Sys_Menu' 、 PrivilegeOperation='Permit'且if PrivilegeMaster字段且if PrivilegeMaster字段
      1）如果是CF_User,则判断PrivilegeMasterKey是否等于在第一步中查找的UserID,是就并入查询结果集
      2）如果PrivilegeMaster字段是CF_Role,则判断PrivilegeMasterKey是否等于在第二步中查找的RoleID,是就并入查询结果集
    

<pre>
查询代码：
select  M.MenuID,M.MenuNo,M.MenuName
from cf_privilege P left join sys_menu M on P.PrivilegeAccessKey=M.MenuID and P.PrivilegeAccess='Sys_Menu'
where (
( P.PrivilegeMaster='CF_User' AND P.PrivilegeMasterKey=(select U.UserID from cf_user U where U.LoginName='test1')) 
or (P.PrivilegeMaster='CF_Role' and P.PrivilegeMasterKey IN
(select U_R.RoleID
from cf_role R,cf_user U,cf_userrole U_R 
where U.UserID=U_R.UserID and R.RoleID=U_R.RoleID and U.LoginName='test1')
)
)   and PrivilegeOperation='Permit' and P.PrivilegeAccess='Sys_Menu';
</pre>

![p1](images/1.png)


##查询用户test1可以对订单(order)页面中的操作权限(sys_button):
    伪代码：
    1.在cf_role中查找LoginName为test1的用户的UserID
    2.在cf_userrole中查找test1的角色的RoleID
    3.cf_privilege与sys_menu进行左连接
    4.在cf_privilege中遍历，P.PrivilegeAccess='Sys_Button' 、PrivilegeOperation='Permit' 、M.MenuName='订单' 、sys_Menu.MenuNo=sys_Menu.MenuNo且if PrivilegeMaster字段   
      1）如果是CF_User,则判断PrivilegeMasterKey是否等于在第一步中查找的UserID,是就并入查询结果集
      2）如果PrivilegeMaster字段是CF_Role,则判断PrivilegeMasterKey是否等于在第二步中查找的RoleID,是就并入查询结果集
      
      
  
<pre>
查询代码：
select B.BtnName,B.BtnID
from cf_privilege P left join sys_button B on P.PrivilegeAccessKey=B.BtnID and P.PrivilegeAccess='Sys_Button',sys_menu M
where (
( P.PrivilegeMaster='CF_User' AND P.PrivilegeMasterKey=(select U.UserID from cf_user U where U.LoginName='test1') ) 
or (P.PrivilegeMaster='CF_Role' and P.PrivilegeMasterKey =
(select U_R.RoleID
from cf_role R,cf_user U,cf_userrole U_R 
where U.UserID=U_R.UserID and R.RoleID=U_R.RoleID and U.LoginName='test1')
)
) and  M.MenuName='订单' and B.MenuNo=M.MenuNo  and PrivilegeOperation='Permit';
</pre>

![p2](images/2.png)
