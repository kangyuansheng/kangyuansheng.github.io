---
title: Spring Boot系列之使用篇:6.使用 Spring Boot上传文件
date: 2019-03-25 22:25:00
author: 康源晟
top: false
cover: true
toc: false
mathjax: false
summary: 使用 Spring Boot上传文件
categories: Spring Boot
tags:
  - Spring Boot
---

### 文件上传示例

Spring Boot文件上传，不需要什么特别的配置。在Controller中，将上传的文件映射到`MultipartFile`。

```java
@Controller
public class UploadController {
    private static final Logger LOGGER = LoggerFactory.getLogger(UploadController.class);

    @GetMapping("/upload")
    public String upload() {
        return "upload";
    }

    @PostMapping("/upload")
    @ResponseBody
    public String upload(@RequestParam("file") MultipartFile file) {
        if (file.isEmpty()) {
            return "上传失败，请选择文件";
        }

        String fileName = file.getOriginalFilename();
        String filePath = "/Users/itinypocket/workspace/temp/";
        File dest = new File(filePath + fileName);
        try {
            file.transferTo(dest);
            LOGGER.info("上传成功");
            return "上传成功";
        } catch (IOException e) {
            LOGGER.error(e.toString(), e);
        }
        return "上传失败！";
    }

}

@PostMapping("/multiUpload")
@ResponseBody
public String multiUpload(HttpServletRequest request) {
    List<MultipartFile> files = ((MultipartHttpServletRequest) request).getFiles("file");
    String filePath = "/Users/itinypocket/workspace/temp/";
    for (int i = 0; i < files.size(); i++) {
        MultipartFile file = files.get(i);
        if (file.isEmpty()) {
            return "上传第" + (i++) + "个文件失败";
        }
        String fileName = file.getOriginalFilename();

        File dest = new File(filePath + fileName);
        try {
            file.transferTo(dest);
            LOGGER.info("第" + (i + 1) + "个文件上传成功");
        } catch (IOException e) {
            LOGGER.error(e.toString(), e);
            return "上传第" + (i++) + "个文件失败";
        }
    }

    return "上传成功";

}
```

### 超过最大上传大小

要处理最大上传大小超出异常，请声明一个`@ControllerAdvice`并捕获`MultipartException`。

```java
@ControllerAdvice
public class GlobalExceptionHandler {

    //http://jira.spring.io/browse/SPR-14651
    //4.3.5 supports RedirectAttributes redirectAttributes
    @ExceptionHandler(MultipartException.class)
    public String handleError1(MultipartException e, RedirectAttributes redirectAttributes) {

        redirectAttributes.addFlashAttribute("message", e.getCause().getMessage());
        return "redirect:/uploadStatus";

    }

}
```

### Multipart文件大小

默认情况下，Spring Boot max文件上传大小为`1MB`，可以通过以下应用程序属性来配置它的值，*application.properties*

```properties
#search multipart
spring.http.multipart.max-file-size=10MB
spring.http.multipart.max-request-size=10MB
```

