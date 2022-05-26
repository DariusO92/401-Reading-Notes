# Day 9 Reading Notes

##  High-level HTTP

- For the layman, an Internet Protocol Address is a numeric identifier for a computer, server, or other resource connected to a TCP/IP network. If you have never seen those terms I would suggest reading a primer on how the internet works before proceeding, because this post is a breakdown of a portion of that. ↩
This is a server that serves a collection of hostnames and their correlated IPs. ↩
If the request or response is greater than the size of a single packet, the browser uses a TCP request instead. This will happen with IPv6 and DNSSEC responses. UDP is a lightweight protocol that optimizes for speed, with the tradeoff being that it offers no guarantees in terms of delivery or order. It is well-suited for use-cases where error-checking can be outside the network interface and packet-loss is preferable to delayed delivery. There is no handshake, so there is no acknowledgement other than a response being sent and received. ↩
Shoutout to E.O. Stinson on Quora for being the only source to mention this (that I could find). Much of his answer on Quora informed my notes. ↩
HTTP (Hyper*text **Transfer **Protocol) is an “application-layer protocol that generally assumes use of TCP as its “transport-layer protocol (Although there are implementations that can use protocols like UDP). We won’t go into what this means too much, but it’s good to understand that everything happening when we’re discussing HTTP is usually happening a layer *above the one we were previously discussing, which means we can take more things for granted. ↩
TCP (Transmission Control Protocol) ↩
which stands for “Synchronize” ↩
This is a packet that doesn’t contain request/response-specific information, and is simply used to manage the transaction ↩
which stands for “Synchronize Acknowledgment” ↩
I guess they got lazy with the names :D ↩
also called a connection parameter ↩
Although the implementation varies and can be condensed into 3 steps ↩


##   HTTP Request

- The HttpUrlConnection class allows us to perform basic HTTP requests without the use of any additional libraries. All the classes that we need are part of the java.net package.
The disadvantages of using this method are that the code can be more cumbersome than other HTTP libraries and that it does not provide more advanced functionalities such as dedicated methods for adding headers or authentication.
- We can create an HttpUrlConnection instance using the openConnection() method of the URL class. Note that this method only creates a connection object but doesn't establish the connection yet.
  The HttpUrlConnection class is used for all types of requests by setting the requestMethod attribute to one of the values: GET, POST, HEAD, OPTIONS, PUT, DELETE, TRACE.

  Let's create a connection to a given URL using GET method:

  URL url = new URL("http://example.com");
  HttpURLConnection con = (HttpURLConnection) url.openConnection();
  con.setRequestMethod("GET");
- If we want to add parameters to a request, we have to set the doOutput property to true, then write a String of the form param1=value¶m2=value to the OutputStream of the HttpUrlConnection instance:

  Map<String, String> parameters = new HashMap<>();
  parameters.put("param1", "val");

  con.setDoOutput(true);
  DataOutputStream out = new DataOutputStream(con.getOutputStream());
  out.writeBytes(ParameterStringBuilder.getParamsString(parameters));
  out.flush();
  out.close();
  To facilitate the transformation of the parameter Map, we have written a utility class called ParameterStringBuilder containing a static method, getParamsString(), that transforms a Map into a String of the required format:

  public class ParameterStringBuilder {
  public static String getParamsString(Map<String, String> params)
  throws UnsupportedEncodingException{
  StringBuilder result = new StringBuilder();

        for (Map.Entry<String, String> entry : params.entrySet()) {
          result.append(URLEncoder.encode(entry.getKey(), "UTF-8"));
          result.append("=");
          result.append(URLEncoder.encode(entry.getValue(), "UTF-8"));
          result.append("&");
        }

        String resultString = result.toString();
        return resultString.length() > 0
          ? resultString.substring(0, resultString.length() - 1)
          : resultString;
    }
}
- Adding headers to a request can be achieved by using the setRequestProperty() method:
  con.setRequestProperty("Content-Type", "application/json");
  To read the value of a header from a connection, we can use the getHeaderField() method:

  String contentType = con.getHeaderField("Content-Type");
  
- HttpUrlConnection class allows setting the connect and read timeouts. These values define the interval of time to wait for the connection to the server to be established or data to be available for reading.

  To set the timeout values, we can use the setConnectTimeout() and setReadTimeout() methods:

  con.setConnectTimeout(5000);
  con.setReadTimeout(5000);
