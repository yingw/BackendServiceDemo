# BackendServiceDemo
This application was generated using JHipster 5.0.2, you can find documentation and help at [https://www.jhipster.tech/documentation-archive/v5.0.2](https://www.jhipster.tech/documentation-archive/v5.0.2).


## 创建项目
```
jhipster --skip-client
```


```
(*) API first development using OpenAPI-generator
 
 Would you like to enable internationalization support? No
```

直接运行
```
mvn
```

可访问链接：
http://localhost:8080/v2/api-docs

认证：
http://localhost:8080/api/authenticate

[{"key":"Content-Type","value":"application/json","description":""}]

body raw json
{
  "username": "admin",
  "password": "admin",
  "rememberMe": true
}


http://localhost:8080/api/users
提交内容
```
Authorization=Bearer eyJhbGciOiJIUzUxMiJ9.eyJzdWIiOiJhZG1pbiIsImF1dGgiOiJST0xFX0FETUlOLFJPTEVfVVNFUiIsImV4cCI6MTUzMzcwNzYwM30.QSw9Oorc_i9i7yWcvTRPJbTcmfg-MY1KJUoyLAtPTK25b0Ai-7VaHWwq5_hqwongPyVZTOyg2A73xYKate6MMw
```

http://localhost:8080/api/account

## 临时去掉安全认证
SecurityConfiguration
```

//            .antMatchers("/api/**").authenticated()
            .antMatchers("/api/**").permitAll()
```

还要去掉 UserResource 里面所有的 `@Secured` 注解

## 设计业务模型

用在线 JDL Studio 工具进行模型设计：https://start.jhipster.tech/jdl-studio/

然后下载 jdl 文件导入：

jhipster-jdl.jh
```
entity Region {
	regionName String
}

entity Country {
	countryName String
}

// an ignored comment
/** not an ignored comment */
entity Location {
	streetAddress String,
	postalCode String,
	city String,
	stateProvince String
}

entity Department {
	departmentName String required
}

/**
 * Task entity.
 * @author The JHipster team.
 */
entity Task {
	title String,
	description String
}

/**
 * The Employee entity.
 */
entity Employee {
	/**
	* The firstname attribute.
	*/
	firstName String,
	lastName String,
	email String,
	phoneNumber String,
	hireDate Instant,
	salary Long,
	commissionPct Long
}

entity Job {
	jobTitle String,
	minSalary Long,
	maxSalary Long
}

entity JobHistory {
	startDate Instant,
	endDate Instant,
	language Language
}

enum Language {
    FRENCH, ENGLISH, SPANISH
}

relationship OneToOne {
	Country{region} to Region
}

relationship OneToOne {
	Location{country} to Country
}

relationship OneToOne {
	Department{location} to Location
}

relationship ManyToMany {
	Job{task(title)} to Task{job}
}

// defining multiple OneToMany relationships with comments
relationship OneToMany {
	Employee{job} to Job,
	/**
	* A relationship
	*/
	Department{employee} to
	/**
	* Another side of the same relationship
	*/
	Employee
}

relationship ManyToOne {
	Employee{manager} to Employee
}

// defining multiple oneToOne relationships
relationship OneToOne {
	JobHistory{job} to Job,
	JobHistory{department} to Department,
	JobHistory{employee} to Employee
}

// Set pagination options
paginate JobHistory, Employee with infinite-scroll
paginate Job with pagination

dto * with mapstruct

// Set service options to all except few
service all with serviceImpl except Employee, Job
// Set an angular suffix
angularSuffix * with mySuffix

```

导入命令
```
jhipster import-jdl "d:\Projects\JHipster\jhipster-jdl.jh"
```

重启项目后测试：

查询
```
curl -X GET http://localhost:8080/api/regions
```

新建
```
curl -X POST -H "Content-Type:application/json" -H "Accept:application/json" -d '{"regionName":"China"}'  http://localhost:8080/api/regions
```

删除
```
curl -X DELETE http://localhost:8080/api/regions/3
```

## Swagger UI

加上 Swagger UI 支持
pom.xml
```
        <springfox-swagger.version>2.9.2</springfox-swagger.version>
        <dependency>
            <groupId>io.springfox</groupId>
            <artifactId>springfox-swagger-ui</artifactId>
            <version>${springfox-swagger.version}</version>
        </dependency>
```

一些 Swagger 服务声明的属性可以在 application.yml 里面配置
```
    swagger:
        default-include-pattern: /api/.*
        title: BackendServiceDemo API
        description: BackendServiceDemo API 文档
        version: 0.0.1
        terms-of-service-url:
        contact-name: 殷国伟
        contact-url:
        contact-email: yinguowei@cn.wilmar-intl.com
        license: Wilmar WPL
        license-url: http://www.wilmar-international.com/licenses/LICENSE-2.0
```

给接口和参数增加说明
```
    @ApiOperation("新建区域地区")
    @PostMapping("/regions")
    @Timed
    public ResponseEntity<RegionDTO> createRegion(
        @ApiParam(name = "regionDTD", value = "地区传输对象", required = true)
        @RequestBody RegionDTO regionDTO) throws URISyntaxException {
```
