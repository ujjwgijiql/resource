# lambda

__List<>转化成Map<,List<>>__
```java
    List<Person> results = new ArrayList<Person>();
    results.add(new Person("Elsdon", "Jaycob", "Java programmer", "male", 43, 2000));
    results.add(new Person("Tamsen", "Brittany", "Java programmer", "female", 23, 1500));

    Map<Integer, List<String>> result = persons
        .stream()
        .collect(Collectors.groupingBy(
            p -> (p.getAge() / 10) * 10, // some integer division trick
            Collectors.mapping(
                p -> p.getFirstName() + " " + p.getLastName() +":" + p.getSalary(),
                Collectors.toList()
            )
        ));
```