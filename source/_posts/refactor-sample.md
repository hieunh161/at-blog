---
title: Ví dụ về Refactoring
date: 2016-11-03 09:32:09
category: software
tags: 
- refactoring
- memo
---

Chúng ta bắt đầu với ví dụ:

```java
package vn.com.ndd.payroll;

public class PayCalculator {
	public static float calculate(double hours, double rate,
			boolean isHourlyWorker) {
		if (hours < 0 || hours > 80) {
			throw new RuntimeException("Hours out of range: " + hours);
		}
		float wages = 0;

		if (hours > 40) {
			double overTimeHours = hours - 40;
			if (isHourlyWorker) {
				wages += (overTimeHours * 1.5) * rate;
			} else {
				wages += overTimeHours * rate;
			}
			hours -= overTimeHours;
		}
		wages += hours * rate;
		return wages;
	}
}
```

<!--more -->
## Bước 1 tạo unit test 

Để có thể refactor hiệu quả chúng ta sẽ viết UT cho source này.

```java
package vn.com.ndd.payroll;

import org.junit.Assert;
import org.junit.Test;

public class PayCalculatorTest {
	private void assertPay(double expectPay, double actualPay){
		Assert.assertEquals(expectPay, actualPay, 0.001);
	}
	
	@Test(expected = RuntimeException.class)
	public void shouldThrowExceptionWhenInvalidMinusValue() {
		PayCalculator.calculate(-0.1, 0, true);
	}

	@Test(expected = RuntimeException.class)
	public void shouldThrowExceptionWhenInvalidBiggerThan80() {
		PayCalculator.calculate(80.1, 0, true);
	}

	@Test
	public void shouldBe0WhenWorkHourIs0() {
		assertPay(0, PayCalculator.calculate(0, 10, true));
		assertPay(0, PayCalculator.calculate(0, 10, false));
	}
	
	@Test
	public void shouldNotCalculateOvertimeWhenWorkRegularHours() {
		assertPay(300, PayCalculator.calculate(30, 10, true));
		assertPay(300, PayCalculator.calculate(30, 10, false));
	}
	
	@Test
	public void shouldCalculateOvertimeWhenWorkHoursGreaterThan40() {
		assertPay(550, PayCalculator.calculate(50, 10, true));
		assertPay(500, PayCalculator.calculate(50, 10, false));
	}
}
```
## Refactoring

### Step 1 : Phân tích và viết lại code dễ hiểu hơn
Đọc qua có thể thấy thuật toán ở đây như sau:
> Giá trị valid là từ 0 -> 80. Nếu lớn hơn 40h sẽ được tính overtime. Chế độ overtime được quyết định bởi dạng hợp đồng. Nếu nhân viên làm việc theo giờ sẽ được tính với giá 1.5 còn bình thường vẫn 1. Hay nói cách khác, việc tính với rate khác chỉ có tác dụng với lao động theo giờ còn các chế độ khác không có gì khác biệt so với thông thường.

Chúng ta có thể viết lại code theo cách dưới đây:
1. Biến đổi logic theo thuật toán phía trên
2. Tách các biến hardcode cho dễ hiểu hơn
3. Chạy lại testcase để đảm bảo UT vẫn chạy đúng

```java
package vn.com.ndd.payroll;

public class PayCalculator {
	private static final double OVERTIME_BONUS_RATE = 0.5;
	private static final int OVERTIME_LIMIT = 80;
	private static final int OVERTIME_THRESHOLD = 40;

	public static double calculate(double hours, double rate,
			boolean isHourlyWorker) {
		if (hours < 0 || hours > OVERTIME_LIMIT) {
			throw new RuntimeException("Hours out of range: " + hours);
		}

		if (isHourlyWorker) {
			double overTime = Math.max(0, hours - OVERTIME_THRESHOLD);
			return hours * rate + overTime * rate * OVERTIME_BONUS_RATE;
		} else {
			return hours * rate;
		}
	}
}

```

Nếu bạn nào không thích sử dụng thêm thư viện Math để tính overTimeHours thì có thể sử dụng toán tử một ngôi

```java
// Sử dụng math
double overTime = Math.max(0, hours - OVERTIME_THRESHOLD);
// sử dụng toán tử 
double overTime = hours - OVERTIME_THRESHOLD > 0? hours - OVERTIME_THRESHOLD : 0;
```

### Step 2 : Bỏ tham số boolean

Hãy luôn nhớ rằng, tham số flag dạng boolean luôn là một cách code tồi, vì nó là dấu hiệu của sự vi phạm quy tắc "single responsibility". Bởi vì trong đoạn code sau đó sẽ thường đi kèm if(A) do B else do C nghĩa là nó sẽ làm 2 việc.
Đầu tiên việc dễ dàng nhất để có thể bỏ được biến boolean trong trường hợp này là chuyển nó thành biến của hàm như sau. 

```java
public class PayCalculator {
	private static final double OVERTIME_BONUS_RATE = 0.5;
	private static final int OVERTIME_LIMIT = 80;
	private static final int OVERTIME_THRESHOLD = 40;
	
	private boolean isHourlyWorker;
	
	public PayCalculator(boolean isHourlyWorker){
		this.isHourlyWorker = isHourlyWorker;
	}

	public double calculate(double hours, double rate) {
		if (hours < 0 || hours > OVERTIME_LIMIT) {
			throw new RuntimeException("Hours out of range: " + hours);
		}

		if (isHourlyWorker()) {
			double overTime = Math.max(0, hours - OVERTIME_THRESHOLD);
			return hours * rate + overTime * rate * OVERTIME_BONUS_RATE;
		} else {
			return hours * rate;
		}
	}
	
	public boolean isHourlyWorker() {
		return isHourlyWorker;
	}
}
```

Điều này dẫn đến cần phải thay đổi source UT như sau:

```java
package vn.com.ndd.payroll;

import org.junit.Assert;
import org.junit.Test;

public class PayCalculatorTest {
	PayCalculator hourlyWorker = new PayCalculator(true);
	PayCalculator nonHourlyWoker = new PayCalculator(false);
	
	private void assertPay(double expectPay, double actualPay){
		Assert.assertEquals(expectPay, actualPay, 0.001);
	}
	
	@Test(expected = RuntimeException.class)
	public void shouldThrowExceptionWhenInvalidMinusValue() {
		hourlyWorker.calculate(-0.1, 0);
	}

	@Test(expected = RuntimeException.class)
	public void shouldThrowExceptionWhenInvalidBiggerThan80() {
		hourlyWorker.calculate(80.1, 0);
	}

	@Test
	public void shouldBe0WhenWorkHourIs0() {
		assertPay(0, hourlyWorker.calculate(0, 10));
		assertPay(0, nonHourlyWoker.calculate(0, 10));
	}
	
	@Test
	public void shouldNotCalculateOvertimeWhenWorkRegularHours() {
		assertPay(300, hourlyWorker.calculate(30, 10));
		assertPay(300, nonHourlyWoker.calculate(30, 10));
	}
	
	@Test
	public void shouldCalculateOvertimeWhenWorkHoursGreaterThan40() {
		assertPay(550, hourlyWorker.calculate(50, 10));
		assertPay(500, nonHourlyWoker.calculate(50, 10));
	}
}
```

### Step 3 : Polymorphism

Nhìn vào UT chúng ta đang tạo ra 2 biến test của cùng một hàm với 2 flag true false. Điều này gợi ý chúng ta có thể sử dụng tính đa hình ở đây để tiếp tục refactor source như sau:

```java
package vn.com.ndd.payroll;

public class ContractorCalculator extends PayCalculator {
	public ContractorCalculator() {
		super(false);
	}
}
```

```java
package vn.com.ndd.payroll;

public class HourlyCalculator extends PayCalculator{
	public HourlyCalculator() {
		super(true);
	}
}
```
class test sẽ trở thành:

```java
public class PayCalculatorTest {
	PayCalculator hourlyWorker = new HourlyCalculator();
	PayCalculator nonHourlyWoker = new ContractorCalculator();
	//...
```

Từ đây chúng ta có thể chuyển hàm tính toán vào các class con và class PayCalculator trở thành class abstract và source chúng ta trở thành:

```java
package vn.com.ndd.payroll;

public abstract class PayCalculator {
	protected static final double OVERTIME_BONUS_RATE = 0.5;
	protected static final int OVERTIME_LIMIT = 80;
	protected static final int OVERTIME_THRESHOLD = 40;
	
	public abstract double calculate(double hours, double rate);
	
	protected void validateHours(double hours) {
		if (hours < 0 || hours > OVERTIME_LIMIT) {
			throw new RuntimeException("Hours out of range: " + hours);
		}
	}
}
```
```java
package vn.com.ndd.payroll;

public class ContractorCalculator extends PayCalculator {
	public ContractorCalculator() {
		super();
	}
	
	public double calculate(double hours, double rate) {
		validateHours(hours);
		return hours * rate;
	}
}
```
```java
package vn.com.ndd.payroll;

public class HourlyCalculator extends PayCalculator {
	public HourlyCalculator() {
		super();
	}

	public double calculate(double hours, double rate) {
		validateHours(hours);
		double overTime = Math.max(0, hours - OVERTIME_THRESHOLD);
		return hours * rate + overTime * rate * OVERTIME_BONUS_RATE;
	}
}
```

## Design Pattern?

Nếu bạn muốn một bước xa hơn nữa, với những ví dụ phức tạp hơn ở đây chúng ta có thể sử dụng template method để tránh việc phải gọi lại validateHours ở các class con như sau:

```java
package vn.com.ndd.payroll;

public abstract class PayCalculator {
	protected static final double OVERTIME_BONUS_RATE = 0.5;
	protected static final int OVERTIME_LIMIT = 80;
	protected static final int OVERTIME_THRESHOLD = 40;
	
	public final double calculate(double hours, double rate){
		validateHours(hours);
		return calculatePay(hours, rate);
	}
	
	public abstract double calculatePay(double hours, double rate);
	
	protected void validateHours(double hours) {
		if (hours < 0 || hours > OVERTIME_LIMIT) {
			throw new RuntimeException("Hours out of range: " + hours);
		}
	}
}
```
```java
package vn.com.ndd.payroll;

public class ContractorCalculator extends PayCalculator {
	public ContractorCalculator() {
		super();
	}
	
	public double calculatePay(double hours, double rate) {
		return hours * rate;
	}
}
```
```java
package vn.com.ndd.payroll;

public class HourlyCalculator extends PayCalculator {
	public HourlyCalculator() {
		super();
	}

	public double calculatePay(double hours, double rate) {
		double overTime = Math.max(0, hours - OVERTIME_THRESHOLD);
		return hours * rate + overTime * rate * OVERTIME_BONUS_RATE;
	}
}
```
Final class diagram:

![UML](https://c2.staticflickr.com/6/5514/30744617115_2e424bc5d0_o.png)

Final source [here](https://github.com/hieunh161/Refactoring)
Refactoring is not only the art but also the money!




