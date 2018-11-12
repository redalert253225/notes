更改 view 的名稱,view_menu Error
--
```
builtin.AttributeError
AttributeError: view_menu
```

flask_appbuilder/security/manager.py:
```
def _has_view_access(self, user, permission_name, view_name):
        roles = user.roles
        for role in roles:
            permissions = role.permissions
            if permissions:
                for permission in permissions:
                    if  (hasattr(permission,'view_menu')) and (view_name == permission.view_menu.name) and (permission_name == permission.permission.name): #更改這一行,跳過有問題的permission
                        return True
        return False
```

右上角icon 不顯示
--
```
"GET /users/list/static/img/my_custom_icon.png HTTP/1.1" 404 -
```

flask_appbuilder/templates/appbuilder/navbar.html:
```
          {% if appbuilder.app_icon %}
              <a class="navbar-brand" href="{{appbuilder.get_url_for_index}}">
              <img src="{{appbuilder.get_url_for_index}}{{appbuilder.app_icon}}"  height="100%" width="auto">  <!--change this line-->
              </a>
          {% else %}
              <span class="navbar-brand">
              <a href="{{appbuilder.get_url_for_index}}">
              {{ appbuilder.app_name }}
              </a>
```
