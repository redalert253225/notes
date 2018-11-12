flask  appbuilder :https://flask-appbuilder.readthedocs.io/en/latest/index.html

view = view class  + model + form + html + widgets

----
define your database structure

app/models.py:
```
from mongoengine import Document,EmbeddedDocument
from mongoengine import DateTimeField, StringField, ReferenceField, ListField,IntField
from flask_babel import lazy_gettext as _

class MyModel(Document):
    First =  StringField( required=True )
    Second =  StringField( required=True )
    meta = {
        "collection" : "Number",
        "max_size" : 40000000
    }
```

define your html

app/templates/my.html:
```
{% extends "appbuilder/general/model/list.html" %}

{% block list_search %}
    {{ super() }}
{% endblock %}

{% block list_list  %}
    {{Tatol}} #變數
    #...
    {{ super() }}
{% endblock %}
```

define your widgets on your html

app/templates/widgets/myWidget.html:
```
# 此為原樣板的程式碼   你可以自己改造
{% import 'appbuilder/general/lib.html' as lib %}
{% extends 'appbuilder/general/widgets/base_list.html' %}

    {% block begin_content scoped %}
        <div class="table-responsive">
        <table class="table table-bordered table-hover">
    {% endblock %}

    {% block list_header %}
        {{ super() }}
    {% endblock %}

    {% block begin_loop_header scoped %}
        <thead>
        <tr>
        {% if actions %}
        <th class="action_checkboxes">
            <input id="check_all" class="action_check_all" name="check_all" type="checkbox">
        </th>
        {% endif %}

        {% if can_show or can_edit or can_delete %}
            <th class="col-md-1 col-lg-1 col-sm-1" ></th>
        {% endif %}

        {% for item in include_columns %}
            {% if item in order_columns %}
                {% set res = item | get_link_order(modelview_name) %}
                    {% if res == 2 %}
                    <th><a href={{ item | link_order(modelview_name) }}>{{label_columns.get(item)}}
                    <i class="fa fa-chevron-up pull-right"></i></a></th>
                {% elif res == 1 %}
                    <th><a href={{ item | link_order(modelview_name) }}>{{label_columns.get(item)}}
                    <i class="fa fa-chevron-down pull-right"></i></a></th>
                {% else %}
                    <th><a href={{ item | link_order(modelview_name) }}>{{label_columns.get(item)}}
                    <i class="fa fa-arrows-v pull-right"></i></a></th>
                {% endif %}
            {% else %}
                <th>{{label_columns.get(item)}}</th>
            {% endif %}
        {% endfor %}
        </tr>
        </thead>
    {% endblock %}

    {% block begin_loop_values %}
        {% for item in value_columns %}
            {% set pk = pks[loop.index-1] %}
            <tr>
                {% if actions %}
                <td>
                    <input id="{{pk}}" class="action_check" name="rowid" value="{{pk}}" type="checkbox">
                </td>
                {% endif %}
                {% if can_show or can_edit or can_delete %}
                    <td><center>
                    {{ lib.btn_crud(can_show, can_edit, can_delete, pk, modelview_name, filters) }}
                    </center></td>
                {% endif %}
                {% for value in include_columns %}
                    {% set formatter = formatters_columns.get(value) %}
                    {% if formatter %}
                        <td>{{ formatter(item[value]) }}</td>
                    {% else %}
                        <td>{{ item[value] }}</td>
                    {% endif %}
                {% endfor %}
            </tr>
        {% endfor %}
    {% endblock %}

    {% block end_content scoped %}
        </table>
        </div>
    {% endblock %}
```

app/Widgets.py:
```
from flask_appbuilder.widgets import ListWidget

class MyWidget(ListWidget):
    template = 'widgets/myWidget.html'
```

define your view class type  ,and import your define

app/viwe.py:
```
from flask_appbuilder import AppBuilder, expose, BaseView
from app import appbuilder
from flask_appbuilder.models.mongoengine.interface import MongoEngineInterface
from .model  import MyModel
from .widgets import  MyWidget

class MyView(ModelView):  #選擇你要繼承的框架類別
    datamodel = MongoEngineInterface(MyModel)
    extra_args = {'Tatol': 0 } #將此字典傳至頁面上顯示
    list_widget = MyWidget
    list_template = 'my.html'
    #..

appbuilder.add_view( MyView, "My  View", icon="fa-th-list", label=_("Traffic Flow"),category="MyDataBase")
#在標題欄增加你的頁面選項   第一個第二個參數不能一樣  否則會BUg  category為所屬的列表名稱    
```
