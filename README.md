# OpenPojo [![Build Status](https://travis-ci.org/oshoukry/openpojo.svg?branch=master)](https://travis-ci.org/oshoukry/openpojo)

POJO Testing &amp; Identity Management Made Trivial 

#### Testing Example
```java
public class PojoTest {
  // Configured for expectation, so we know when a class gets added or removed.
  private static final int EXPECTED_CLASS_COUNT = 1;

  // The package to test
  private static final String POJO_PACKAGE = "com.openpojo.samplepojo";

  private List<PojoClass> pojoClasses;
  private PojoValidator pojoValidator;

  @Before
  public void setup() {
    pojoClasses = PojoClassFactory.getPojoClasses(POJO_PACKAGE, new FilterPackageInfo());
    pojoValidator = new PojoValidator();

    // Add Rules to validate structure for POJO_PACKAGE
    pojoValidator.addRule(new GetterMustExistRule());
    pojoValidator.addRule(new SetterMustExistRule());
    pojoValidator.addRule(/* ... See com.openpojo.validation.rule.impl for more ...*/);

    // Add Testers to validate behaviour for POJO_PACKAGE
    pojoValidator.addTester(new SetterTester());
    pojoValidator.addTester(new GetterTester());
    pojoValidator.addTester(/* ... See com.openpojo.validation.test.impl for more ...*/);
  }

  @Test
  public void ensureExpectedPojoCount() {
    Affirm.affirmEquals("Classes added / removed?", EXPECTED_CLASS_COUNT, pojoClasses.size());
  }

  @Test
  public void testPojoStructureAndBehavior() {
    for (PojoClass pojoClass : pojoClasses) {
        pojoValidator.runValidation(pojoClass);
    }
  }
}
```

#### Identity Management Example
```java
public class Person {
  @BusinessKey(caseSensitive = false)  //Configure your field(s)
  private String lastname;

  @Override
  public boolean equals(Object obj) {
    return BusinessIdentity.areEqual(this, obj);
  }

  @Override
  public int hashCode() {
    return BusinessIdentity.getHashCode(this);
  }

  @Override
  public String toString() {
      return BusinessIdentity.toString(this);
  }
}
```