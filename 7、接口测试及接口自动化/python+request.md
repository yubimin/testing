# Python操作excel表格

## python：利用xlrd模块操作excel

```python
from xlrd import open_workbook
# data=open_workbook('../testFile/userCase.xlsx')
# tables = data.sheets()[0]
# print(tables.nrows)
# print(tables.cell_value(1,2))

class OperationExcel:
    def __init__(self,file_name=None,sheet_id=None):
        if file_name:
            self.file_name=file_name
            self.sheet_id=sheet_id
        else:
            self.file_name= '../testFile/userCase.xlsx'
            self.sheet_id = 0

        self.data = self.getdata()
    #获取sheet 内容
    def getdata(self):
        data=open_workbook(self.file_name)
        tables = data.sheets()[self.sheet_id]
        return tables

    #获取单元格行数
    def get_lines(self):
        tables=self.data
        return  tables.nrows

    #获取某一个单元格的内容
    def get_cell_value(self,row,col):
      return  self.data.cell_value(row,col)

#调试
if __name__ == '__main__':
    opers=OperationExcel()
    # print(opers.getdata().nrows)
    print(opers.get_lines())
    print(opers.get_cell_value(1,0))
```

**脚本解析：**

①、刚开始导入的自定义模块 from readConfig import Signup_data,Login_data ，这里我将测试数据的文件路径放在了配置文件里，然后简单封装了读取配置文件的一个readConfig方法，

这样做的好处是降低了维护成本（即使后期相对的有变动，只需要改变配置文件和这个封装的方法，而不用去修改测试脚本），提高了脚本可维护性，重用性和服务迁移的成本。

读取配置文件链接：[python：利用configparser模块读取配置文件](http://www.cnblogs.com/imyalost/p/8857896.html)

可放入配置文件的信息这里举一些例子：数据库连接信息、文件路径、用户名、密码、后台接口等。。。

PS：如果使用GIT做版本控制，文件上传远程仓库后，敏感信息不做管理，这样风险比较大（如何降低这种风险，后续的博客会介绍）。。。

②、如上面的代码所示，我只是简单的写了注册和登录的2个方法，但如果测试数据比较多（存在很多不同excel中）或者测试点比较多，这样就比较臃肿了，可以继续对其进行优化，比如写一个类，

初始化一些共用的信息，每个功能点对应的不同方法只需要几行代码就搞定，还可以从业务角度进行拆分等（后续会不断更新优化后的内容）。。。

**测试数据管理**

**1、使用excel管理测试数据的局限性**

博客开头就提到了，excel只适用于测试用例数据不太多的情况，如果测试数据较多，那么excel的瓶颈也很明显，原因如下：

①、excel单表只能支持65535行，如果测试用例有很多，那么excel就是制约测试用例和测试数据管理的最大问题；

②、数据量大，excel的增删改查不好做，不能做成服务，因为有IO锁，不支持事务，无法多人共用，对后续的自动化集成平台开发带来影响；

**2、优化方案**

①、测试数据存储在专门的测试DB，封装读写数据的方法；

②、多人共用的问题，可以用docker部署高可用的测试环境，每个人都拥有独立的测试环境，做好版本管理；

 如上所示，就是xlrd读取excel数据的简单使用方法以及测试用例数据管理相关的一些思路，仅供参考，具体做法和优化请自行实践。。。



# Python读取json文件

operationJson.py

```
#coding=utf-8
import json

# #加载json文件
# fp=open("../testFile/login.json")
# data = json.load(fp)
# print

class OperationJson:
    def __init__(self):
        self.data=self.read_data()

    #读取json文件
    def read_data(self):
        with open("../testFile/login.json") as fp:
            data= json.load(fp)
            return data
    # 根据关键字获取数据
    def get_data(self,id):
        return self.data[id]
#调试
if __name__ == '__main__':
    opjson=OperationJson()
    print(opjson.read_data()
    print(opjson.get_data('assert'))
```



# 封装用例表格字段定义类

```python
#coding=utf-8
class global_var:
    case_name='0'
    request_way='1'
    url='2'
    run='3'
    header='4'
    case_depend='5'
    data_depend = '6'
    field_depend = '7'
    data='8'
    expect='9'
    result='10'
# 获取case_name
def get_case_name():
    return global_var.case_name

def get_request_way():
    return global_var.request_way

def get_run():
    return global_var.run

def get_url():
    return global_var.url

def get_header():
    return global_var.header

def get_case_depend():
    return global_var.case_depend

def get_data_depend():
    return global_var.data_depend

def get_field_depend():
    return global_var.field_depend

def get_data():
    return global_var.data

def get_expect():
    return global_var.expect

def get_result():
    return global_var.result

def get_header_value():
    header={
        "header":"1234",
        "cookies":"yhhhh"
    }
    return  header
```



# 封装获取表格及json数据类

```python
#coding=utf-8
from util.operation_excel import OperationExcel
import data.data_config as data_config
from util.operation_json import OperationJson
from base import readConfig

localReadConfig = readConfig.ReadConfig()
# global host, port, timeout
# timeout = localReadConfig.get_http("timeout")
# host = localReadConfig.get_http("baseurl")
# port = localReadConfig.get_http("port")

class GetData:
    def __init__(self):
        self.opera_excel=OperationExcel()
        global host, port
        host = localReadConfig.get_http("baseurl")
        port = localReadConfig.get_http("port")
    # 获取excel行数，就是case个数
    def get_case_lines(self):
       return self.opera_excel.get_lines()

    #获取是否执行
    def get_is_run(self,row):
        flag=None
        col = int(data_config.get_run())
        run_model = self.opera_excel.get_cell_value(row,col)
        if run_model == 'yes':
            flag = True
        else:
            flag = False
        return flag

    # 是否携带header
    def is_header(self,row):
        col = int(data_config.get_header())
        header = self.opera_excel.get_cell_value(row,col)
        print (header)
        if header == 'yes':
            return data_config.get_header_value()
        else:
            return None

    # 获取请求方式
    def get_request_method(self,row):
        col = int(data_config.get_request_way())
        request_method = self.opera_excel.get_cell_value(row,col)
        return request_method

    #获取url
    def get_request_url(self,row):

        col = int(data_config.get_url())
        url = host + ':' + port + self.opera_excel.get_cell_value(row,col)
        return url

    #获取请求数据
    def get_request_data(self,row):
        col = int(data_config.get_data())
        data = self.opera_excel.get_cell_value(row,col)
        if data == '':
            return  None
        return data

    #通过获取关键字获取data数据
    def get_data_for_json(self,row):
        opera_json=OperationJson()
        request_data = opera_json.get_data(self.get_request_data(row))
        return request_data
    # 获取预期结果
    def getget_expect_data(self,row):
        col = int(data_config.get_expect())
        expect = self.opera_excel.get_cell_value(row,col)
        if expect == '':
            return None
        return expect

#调试
if __name__ == '__main__':
    opera_excel1 = OperationExcel()
    data= GetData()
    # print(data.get_case_lines())
    # print(data.is_header(1))
    # print(data.get_is_run(1))
    # print(data.get_request_method(1))
    print(data.get_request_url(1))
    # print(data.get_request_data(1))
    # print(data.get_data_for_json(1))
    # print(data.getget_expect_data(1))
    # print(opera_excel1.get_cell_value(1,4))
    # print(data_config.get_header_value())
```

# 发送https请求类的封装

```python
#coding=utf-8
import requests
import json
from base import readConfig
localReadConfig = readConfig.ReadConfig()

timeout = localReadConfig.get_http("timeout")

class RunMethod:

    def post_main(self,url,data,header=None):
        requests.packages.urllib3.disable_warnings()
        res = None
        if header !=None:
            res = requests.post(url=url,data=data,headers=header,timeout=float(timeout),verify=False).json()
        else:
            res = requests.post(url=url, data=data,timeout=float(timeout),verify=False).json()
        return res


    def get_main(self,url,data=None,header=None):
        requests.packages.urllib3.disable_warnings()
        res = None
        if header != None:
            res = requests.get(url=url, data=data, headers=header,timeout=float(timeout),verify=False).json()
        else:
            res = requests.post(url=url, data=data,timeout=float(timeout),verify=False).json()
        return res

    def run_main(self,method,url,data=None,header=None):
        # requests.packages.urllib3.disable_warnings()
        if method == 'post':
            res = self.post_main(url,data,header)
        else:
            res = self.get_main(url,data,header)
        return  res

#调试
if __name__ == '__main__':
    run=RunMethod()
    url="https://172.16.81.199:8888/sso/doLogin.jhtml"
    data={'method': 'login', 'loginName': 'Wangzichen', 'psw': 'Zgy^^123'}
    method='post'
    res = run.run_main('post',"https://172.16.81.199:8888/sso/doLogin.jhtml",data)
    res1 = requests.post(url=url, data=data,verify=False)
    print(res)
    print(res1.json())

```

# 执行调用表格数据执行请求的类封装

```python
#coding=utf-8
from base.runmethod import RunMethod
from data.get_data import GetData

class RunTest:
    def __init__(self):
        self.run_method=RunMethod()
        self.data=GetData()

    #程序执行的主入口
    def run_on_run(self):
        rows_count = self.data.get_case_lines()
        # print(rows_count)
        for i in range(1,rows_count):
            url=self.data.get_request_url(i)
            # print("url:"+url)
            method = self.data.get_request_method(i)
            # print("method:"+method)
            is_run= self.data.get_is_run(i)
            # print(is_run)
            data = self.data.get_data_for_json(i)
            # print(data)
            header = self.data.is_header(i)
            # print(header)
            if is_run:
                #self,method,url,data=None,header=None
                res = self.run_method.run_main(method,url,data,header)
            return res

#调试
if __name__ == '__main__':
    run=RunTest()
    run_method1 = RunMethod()
    res=run.run_on_run()
    print(res)
```



# python自动化框架之mock

```bash
from mock import mock
def mock_test(mock_method,request_data,url,method,response_data):
    mock_method = mock.Mock(return_value=response_data)
    res = mock_method(url,method,request_data)
    return res
```

