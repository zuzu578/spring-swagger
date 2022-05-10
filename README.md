# spring-swagger

spring 에서 swagger 로 api 문서 작성하기 

# maven 

```maven
<!-- swagger -->
        <dependency>
	    <groupId>io.springfox</groupId>
	    <artifactId>springfox-swagger2</artifactId>
	    <version>2.9.2</version> 
	</dependency>
 <dependency>
            <groupId>com.google.guava</groupId>
            <artifactId>guava</artifactId>
            <version>23.0</version>
</dependency>


```
# 설정파일 

``` java
// SwaggerConfiguration.java 

import org.springframework.context.annotation.Bean;

import springfox.documentation.builders.PathSelectors;
import springfox.documentation.spi.DocumentationType;
import springfox.documentation.spring.web.plugins.Docket;
import springfox.documentation.swagger2.annotations.EnableSwagger2;

import springfox.documentation.builders.ApiInfoBuilder;

import springfox.documentation.service.ApiInfo;

@EnableSwagger2
public class SwaggerConfiguration {

    @Bean
    public Docket api() {
    	   return new Docket(DocumentationType.SWAGGER_2)
                   .groupName("00. All Device API REST Service")
                   .apiInfo(apiInfo())
                   .select()
                   .paths(PathSelectors.any())
                   .build();
       }
    
       @Bean
       public Docket newsApiAccelerator() {
           return new Docket(DocumentationType.SWAGGER_2)
                   .groupName("01. Accelerator Guide Program Service")
                   .apiInfo(apiInfo())
                   .select()
                   .paths(PathSelectors.any())
                   .build();
       }
    
       private ApiInfo apiInfo() {
           return new ApiInfoBuilder()
                   .title("표준프레임워크 DeviceAPI 연계서비스 (Hybrid App)")
                   .description("표준프레임워크 하이브리드앱 실행환경  - iOS / Android 하이브리드앱 Rest 서비스")
                   .termsOfServiceUrl("https://www.egovframe.go.kr/wiki/doku.php?id=egovframework:hyb:gate_page")
                   .license("Apache License Version 2.0")
                   .licenseUrl("https://www.egovframe.go.kr")
                   .version("3.10")
                   .build();
       }
}




```

사용 

```java 
/**
	 * 사용자목록을 조회한다. (pageing)
	 * @param userSearchVO 검색조건정보
	 * @param model 화면모델
	 * @return cmm/uss/umt/EgovUserManage
	 * @throws Exception
	 */
	@GetMapping(value = "/uss/umt/EgovUserManageAjax.do")
	@ApiOperation(value = "사용자목록 조회", notes = "사용자목록을 조회한다.")
	@ApiImplicitParams(
	        {
	            @ApiImplicitParam(
	                name = "sbscrbSttus"
	                , value = "sbscrbSttus"
	                , required = false
	                , dataType = "string"
	                , paramType = "path"
	                , defaultValue = "None"
	            )
	        ,
	            @ApiImplicitParam(
	                name = "searchKeyword"
	                , value = "응답 필드 종류"
	                , required = false
	                , dataType = "string"
	                , paramType = "query"
	                , defaultValue = ""
	            )
	           ,
	            @ApiImplicitParam(
		                name = "searchCondition"
		                , value = "검색조건 0 : emplyr_id / 1: user_nm "
		                , required = false
		                , dataType = "string"
		                , paramType = "query"
		                , defaultValue = ""
		            )
	        })
	public ModelAndView selectUserList(@ModelAttribute("userSearchVO") UserDefaultVO userSearchVO, ModelMap model) throws Exception {
		
		ModelAndView modelAndView = new ModelAndView();
    	modelAndView.setViewName("jsonView");
		// 미인증 사용자에 대한 보안처리
		Boolean isAuthenticated = EgovUserDetailsHelper.isAuthenticated();
		//isAuthenticated = true;
		if (!isAuthenticated) {
			modelAndView.addObject("message","미인증된 사용자입니다.");
			return modelAndView;
		}

		List<?> userList = userManageService.selectUserList(userSearchVO);
		model.addAttribute("resultList", userList);
		modelAndView.addObject("resultList",userList);
		

		//사용자상태코드를 코드정보로부터 조회
		ComDefaultCodeVO vo = new ComDefaultCodeVO();
		vo.setCodeId("COM013");
		List<?> emplyrSttusCode_result = cmmUseService.selectCmmCodeDetail(vo);
		modelAndView.addObject("emplyrSttusCode_result",emplyrSttusCode_result);
		
		return modelAndView;
	}

```
