# ElasticSearch query

# **1.입력,조회,수정,삭제**

# 입력(PUT)

`테이블명/_doc/id해당값` 꼴로 query가 만들어집니다.

- 입력
    
    PUT my_index/_doc/1
    {
    "name":"Jongmin Kim",
    "message":"안녕하세요 Elasticsearch"
    }
    
- 결과
    
    {
    "_index" : "my_index",
    "_type" : "_doc",
    "_id" : "1",
    "_version" : 1,
    ***"result" : "created"***,
    "_shards" : {
    "total" : 2,
    "successful" : 1,
    "failed" : 0
    },
    "_seq_no" : 0,
    "_primary_term" : 1
    }
    

**처음으로 도큐먼트를 입력하면 결과에 `"result" : "created"` 로 표시가 됩니다.**

newstest_2021.11.21을 이미 만들었습니다.

- 입력
    
    PUT newstest_2021.11.21/_doc/YqoTRn0B9E55zH30-r_Z
    {
    "name":"안녕",
    "message":"안녕하세요 Elasticsearch"
    }
    
- 결과
    
    {
    "_index" : "newstest_2021.11.21",
    "_type" : "_doc",
    "_id" : "YqoTRn0B9E55zH30-r_Z",
    "_version" : 2,
    ***"result" : "updated"***,
    "_shards" : {
    "total" : 2,
    "successful" : 1,
    "failed" : 0
    },
    "_seq_no" : 31,
    "_primary_term" : 1
    }
    

**동일한 URL에 다른 내용의 도큐먼트를 다시 입력하게 되면 기존 도큐먼트는 삭제되고 새로운 도큐먼트로 덮어씌워지게 됩니다. 이 때는 결과에 `created`가 아닌 `updated`가 표시됩니다.**

- 입력
    
    PUT my_index/_create/1
    {
    "name":"Jongmin Kim",
    "message":"안녕하세요 Elasticsearch"
    }
    
- 결과
    
    {
    "error" : {
    "root_cause" : [
    {
    "type" : "version_conflict_engine_exception",
    "reason" : "[1]: version conflict, document already exists (current version [1])",
    "index_uuid" : "zumjDKU2SvqxYzgDJ1xrhg",
    "shard" : "0",
    "index" : "my_index"
    }
    ],
    "type" : "version_conflict_engine_exception",
    "reason" : "[1]: version conflict, document already exists (current version [1])",
    "index_uuid" : "zumjDKU2SvqxYzgDJ1xrhg",
    "shard" : "0",
    "index" : "my_index"
    },
    "status" : 409
    }
    

**실수로 기존 도큐먼트가 덮어씌워지는 것을 방지하기 위해서는 입력 명령에 _doc 대신 _create 를 사용해서 새로운 도큐먼트의 입력만 허용하는 것이 가능합니다. 입력하려는 도큐먼트 id에 이미 데이터가 있는 경우 아래와 같이 입력 오류가 나게 됩니다.**

# 조회(GET)

`테이블명/_doc/id해당값` 꼴로 query가 만들어집니다.

- 입력
    
    GET my_index/_doc/1
    
- 결과
    
    {
    "_index" : "my_index",
    "_type" : "_doc",
    "_id" : "1",
    "_version" : 1,
    "_seq_no" : 0,
    "_primary_term" : 1,
    "found" : true,
    "_source" : {
    "name" : "Jongmin Kim",
    "message" : "안녕하세요 Elasticsearch"
    }
    }
    

**다양한 정보가 함께 표시되며 문서의 내용은 _source 항목에 나타납니다**.

# 삭제(DELETE)

`테이블명/_doc/id해당값` 꼴로 query가 만들어집니다.

- 입력
    
    DELETE my_index/_doc/1
    
- 결과
    
    {
    "_index" : "my_index",
    "_type" : "_doc",
    "_id" : "1",
    "_version" : 2,
    ***"result" : "deleted"***,
    "_shards" : {
    "total" : 2,
    "successful" : 1,
    "failed" : 0
    },
    "_seq_no" : 1,
    "_primary_term" : 1
    }
    

**한 인덱스 삭제를 하는 코드입니다.**

- 입력
    
    my_index/_doc/1
    
- 결과
    
    {
    "_index" : "my_index",
    "_type" : "_doc",
    "_id" : "1",
    ***"found" : false***
    }
    

**도큐먼트는 삭제되었지만 인덱스는 남아있는 경우 삭제된 도큐먼트를 GET 해서 가져오려고 하면 아래와 같이 my_index/_doc/1 도큐먼트를 못 찾았다는 `"found" : false` 응답을 받습니다. 인덱스는 있으나 입력되지 않은 조회할 때도 마찬가지 입니다.**

- 입력
    
    DELETE my_index
    
- 결과
    
    {
    "acknowledged" : true
    }
    

**이제 `DELETE my_index` 으로 전체 인덱스를 삭제하면 다음과 같이 `"acknowledged" : true` 응답만 리턴됩니다.**

- 입력
    
    GET my_index/_doc/1
    
- 결과
    
    {
    "error" : {
    "root_cause" : [
    {
    "type" : "index_not_found_exception",
    "reason" : "no such index [my_index]",
    "resource.type" : "index_expression",
    "[resource.id](http://resource.id/)" : "my_index",
    "index_uuid" : "*na*",
    "index" : "my_index"
    }
    ],
    "type" : "index_not_found_exception",
    "reason" : "no such index [my_index]",
    "resource.type" : "index_expression",
    "[resource.id](http://resource.id/)" : "my_index",
    "index_uuid" : "*na*",
    "index" : "my_index"
    },
    "status" : 404
    }
    

**삭제된 인덱스 또는 처음부터 없는 인덱스의 도큐먼트를 조회하려고 하면 도큐먼트를 못 찾았다는 `"found" : false` 응답이 아니라 다음과 같이 `"type" : "index_not_found_exception"` , `"status" : 404` 오류가 리턴됩니다.**

# 수정(POST)

`테이블명/_doc` 꼴로 query가 만들어집니다.

- 입력
    
    POST my_index/_doc
    {
    "name":"Jongmin Kim",
    "message":"안녕하세요 Elasticsearch"
    }
    
- 결과
    
    {
    "_index" : "my_index",
    "_type" : "_doc",
    "_id" : "qjEnS30BgDWcSNZI3nqA",
    "_version" : 1,
    "result" : "created",
    "_shards" : {
    "total" : 2,
    "successful" : 1,
    "failed" : 0
    },
    "_seq_no" : 1,
    "_primary_term" : 1
    }
    

**POST 메서드는 PUT 메서드와 유사하게 데이터 입력에 사용이 가능합니다. 도큐먼트를 입력할 때 POST 메서드로 `<인덱스>/_doc` 까지만 입력하게 되면 자동으로 임의의 도큐먼트id 가 생성됩니다. 도큐먼트id의 자동 생성은 PUT 메서드로는 동작하지 않습니다.**

**위 결과에서 도큐먼트 id `"_id" : "qjEnS30BgDWcSNZI3nqA"`가 자동 생성 된 것을 확인할 수 있습니다.**

# _update

`테이블명/_update/id` 꼴로 query가 만들어집니다.

- 입력
    
    POST my_index/_update/1
    {
    "doc": {
    "message":"안녕하세요 Kibana"
    }
    }
    
- 결과
    
    {
    "_index" : "my_index",
    "_type" : "_doc",
    "_id" : "1",
    "_version" : 2,
    "result" : "updated",
    "_shards" : {
    "total" : 2,
    "successful" : 1,
    "failed" : 0
    },
    "_seq_no" : 2,
    "_primary_term" : 1
    }
    

**입력된 도큐먼트를 수정하기 위해서는 기존 도큐먼트의 URL에 변경될 내용의 도큐먼트 내용을 다시 PUT 하는 것으로 대치가 가능합니다. 하지만 필드가 여럿 있는 도큐먼트에서 필드 하나만 바꾸기 위해 전체 도큐먼트 내용을 매번 다시 입력하는 것은 번거로운 작업일 것입니다. 이 때는 `POST <인덱스>/_update/<도큐먼트 id>` 명령을 이용해 원하는 필드의 내용만 업데이트가 가능합니다. 업데이트 할 내용에 "doc" 이라는 지정자를 사용합니다.**

- 입력
    
    GET my_index/_doc/1
    
- 결과
    
    {
    "_index" : "my_index",
    "_type" : "_doc",
    "_id" : "1",
    ***"_version" : 2,***
    "_seq_no" : 2,
    "_primary_term" : 1,
    "found" : true,
    "_source" : {
    "name" : "Jongmin Kim",
    ***"message" : "안녕하세요 Kibana"***
    }
    }
    

**이제 다시 GET 명령으로 `my_index/_doc/1` 도큐먼트를 조회 해 보면 message 필드가 "안녕하세요 Kibana" 로 변경 된 것을 확인할 수 있습니다.**

**위 결과를 보면 `"_version" : 2`로 버전이 증가한 것을 확인할 수 있습니다. _update API 를 사용해서 단일 필드만 수정하는 경우에도 실제로 내부에서는 도큐먼트 전체 내용을 가져와서 **_doc 에서 지정한 내용을 변경한 새 도큐먼트를 만든 뒤 전체 내용을 다시 PUT 으로 입력하는 작업을 진행합니다.**

 

[GitHub - asterinwl/elastic_search](https://github.com/asterinwl/elastic_search)