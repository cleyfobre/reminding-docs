#이뮤터블 #immutable

세팅할 때(생성자 사용할 때) 파라미터를 아래처럼 복사해서 넣고.

```java
public ImmutablePerson(List<String> hobbies) {
    // 입력 받은 리스트를 복사
    this.hobbies = new ArrayList<>(hobbies);
}
```

가져올 때(get 메소드) 필드값을 새로 복사해서 리턴해주면 불변성 유지함.

```java
public List<String> getHobbies() {
    // 내부 리스트의 복사본을 반환
    return new ArrayList<>(hobbies);
}
```

정리하면 아래와 같다.

```java
import java.util.ArrayList;
import java.util.Collections;
import java.util.List;

public final class ImmutablePerson {

    private final List<String> hobbies;

    public ImmutablePerson(List<String> hobbies) {
        // 입력 받은 리스트를 복사
        this.hobbies = new ArrayList<>(hobbies);
    }

    public List<String> getHobbies() {
        // 내부 리스트의 복사본을 반환
        return new ArrayList<>(hobbies);
    }
}

public class Main {
    public static void main(String[] args) {
        List<String> hobbies = new ArrayList<>();
        hobbies.add("Reading");
        hobbies.add("Swimming");

        ImmutablePerson person = new ImmutablePerson(hobbies);
        System.out.println("Before modification: " + person.getHobbies());

        // 외부에서 리스트를 수정
        hobbies.add("Running");
        System.out.println("After external modification: " + person.getHobbies());

        // 반환된 리스트를 수정
        List<String> hobbiesFromPerson = person.getHobbies();
        hobbiesFromPerson.add("Cycling");
        System.out.println("After returned list modification: " + person.getHobbies());
    }
}

```

