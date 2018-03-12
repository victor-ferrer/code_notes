# Load a java.util.properties from File/URL/HDFS

```
/**
* Utility class that provides logic for reading Properties objects from several locations:
* - URL (HTTP/S)
* - File
* - HDFS 
*/
public class PropertiesReader {

    private static final Logger LOGGER = LoggerFactory.getLogger(PropertiesReader.class);
    
    private static final String HTTP_RESOURCE_REGEX = "(http[s]?://.*[:[0-9]+]?/.*)";
    
    /**
     * Gets an InputStream from either a File, URL or HDFS
    public static InputStream getFileInputStream(String path) throws IOException 
    {
        // Try with an HTTP(S) resource
        Pattern httpPat = Pattern.compile(HTTP_RESOURCE_REGEX);
        Matcher httpMatcher = httpPat.matcher(path);
        
        if (httpMatcher.matches()){
            return getHTTPInputStream(httpMatcher.group());
        }
        
        // TODO Copy HDFS block

        // Now try with a regular file
        return new FileInputStream(path);
    }
    
    /**
     * @param path String representing the location of the properties file. Can be HDFS, URL or local file
     *  @return the loaded Properties object
     *  @throws URISyntaxException 
     * @throws IOException 
     */
    public static Properties getProperties(String path) throws IOException
    {
        // Try with an HDFS resource
        Properties toReturn = new Properties();
        toReturn.load(getFileInputStream(path));
        
        return toReturn;
    }

    private static InputStream getHTTPInputStream(String path) throws IOException {
        LOGGER.info(String.format("Trying to open URL at " + path));
        return new URL(path);
    }
}
```