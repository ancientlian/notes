
如果加了`@Provider`则不需要再`Application`中注册

```java
@Slf4j  
@Provider  
public class ExceptionMappingResource implements ExceptionMapper<Exception> {  
    /**  
     * Map an exception to a {@link Response}. Returning  
     * {@code null} results in a {@link Response.Status#NO_CONTENT}  
     * response. Throwing a runtime exception results in a  
     * {@link Response.Status#INTERNAL_SERVER_ERROR} response.  
     *     * @param exception the exception to map to a response.  
     * @return a response mapped from the supplied exception.  
     */    @Override  
    public Response toResponse(Exception exception) {  
        log.error("Exception mapping");  
        return null;  
    }  
}
```
