```
package test.interview;

import java.io.FileInputStream;
import java.io.FileOutputStream;
import java.io.ObjectInputStream;
import java.io.ObjectOutputStream;
import java.util.Date;

public class TransientTest implements java.io.Serializable {
    private static final long serialVersionUID = 1L;
    private Date loggingDate = new Date();
    private String uid;
    private transient String pwd;

    TransientTest(String user, String password) {
        uid = user;
        pwd = password;
    }

    public String toString() {
        String password = null;
        if (pwd == null) {
            password = "NOT SET";
        } else {
            password = pwd;
        }
        return "logon info: \n   " + "user: " + uid + "\n   logging date : " + loggingDate.toString() + "\n   password: " + password;
    }

    public static void main(String[] args) {
        TransientTest logInfo = new TransientTest("Jinder", "Rh-ab703");
        System.out.println(logInfo.toString());
        try {
            ObjectOutputStream o;
            o = new ObjectOutputStream(new FileOutputStream("logInfo.out"));
            o.writeObject(logInfo);
            o.close();
        } catch (Exception e) {
            // deal with exception
        }
        // To read the object back, we can write
        try {
            ObjectInputStream in = new ObjectInputStream(new FileInputStream("logInfo.out"));
            TransientTest logInfo1 = (TransientTest) in.readObject();
            System.out.println(logInfo1.toString());
        } catch (Exception e) {
            // deal with exception
        }
    }
}/*out:
logon info:
    user: Jinder
    logging date : Tue Dec 17 23:39:49 CST 2019
    password: Rh-ab703 
logon info:
    user: Jinder
    logging date : Tue Dec 17 23:39:49 CST 2019
    password: NOT SET*/

```

```
T data = JsonUtil.fromJson(JsonUtil.toJson(rspData), objectType);
Method method = data.getClass().getMethod("getStatus", new Class[]{});
Object value = method.invoke(data, new Object[]{});
String statusValue = String.valueOf(value);
```


https://www.jiweichengzhu.com/article/2940ed65c94f4671ae3f3aa72e168673
