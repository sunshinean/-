文档：http://doc.codingdict.com/elasticsearch/167/  
Elasticsearch-bool组合查询 与或非 ：https://blog.csdn.net/zhanghongzheng3213/article/details/83142400  

```
class ESDataExport:
    """
    连接ES 然后查询数据
    """

    def __init__(self, index_name, index_type, ip,
                 use_ssl=False, user='admin', passwd='admin', cert='172.19.34.25.pem'):
        '''
        :param index_name: 索引名称
        :param index_type: 索引类型
        '''
        self.index_name = index_name
        self.index_type = index_type
        if use_ssl:
            path = os.getcwd()
            cert_cert = os.path.join(path, "key", cert)
            print("证书路径为：" + cert_cert)

            # cert_path = path + '/key'
            # cert_cert = cert_path + '/' + cert
            self.es = Elasticsearch(
                [ip],
                http_auth=(user, passwd),
                port=9200,
                # turn on SSL
                use_ssl=True,
                # make sure we verify SSL certificates
                verify_certs=True,
                # provide a path to CA certs on disk
                ca_certs=cert_cert
            )
        else:
            # 无用户名密码状态
            self.es = Elasticsearch([ip])

    def query_student_data(self, lesson_id, user_id, classes):
        """ 根据一节课的id 和用户id 导出所有的数据 """
        doc = {
            "query": {
                "bool": {
                    "must": [
                        {"match_phrase": {"userId": user_id}},
                        {"match_phrase": {"lessonId": lesson_id}},
                        {"match_phrase": {"numberOfClass": classes}},
                    ]
                }
            },
            "aggs": {
                "max_class_num": {
                    "max": {
                        "field": "numberOfClassTimes"
                    }
                }
            },
            "size": 1000,
        }
        _searched = self.es.search(index=self.index_name, body=doc)
        classes_max = _searched["aggregations"]["max_class_num"]["value"]
        list_data = []
        for hits in _searched['hits']['hits']:
            if hits["_source"]["numberOfClassTimes"] == classes_max:
                list_data.append(reduction_data(hits['_source']))
        return list_data

```
