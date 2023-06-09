# Kiosk, 키오스크 프로그램
<br/>

## 팀 소개

### 팀명 : 어렵자바 <br/>
팀장 : 임채영 (본인)  https://github.com/Chaeyounglim <br/>
팀원 : 김성수님  https://github.com/ksungsu <br/>
팀원 : 전영민님  https://github.com/youngmin13 <br/>
팀원 : 모성민님 https://github.com/Garim12 <br/>
팀원 : 이경식님  https://github.com/leegyeongsik

<br/><br/>

## 프로젝트 소개<br/>
음식점에서 주문할 때 사용하는 **키오스크**를 구현한 프로젝트입니다. <br/>
고객이 원하는 메뉴를 주문하고 해당 주문을 취소할 수 있도록 하였습니다. <br/>
또한, 해당 주문에 대해 완료가 되었는지 여부와 최근 완료가 된 주문을 볼 수 있습니다. <br/><br/>

관리자 페이지를 만들어 주문에 대해 음식점 측에서 해야 할 역할 수행합니다. <br/>
각 주문 별로 주문을 완료하고, 정렬하여 보여주며, 상품 메뉴를 추가 및 삭제합니다. <br/>

<br/><br/>

## 프로젝트의 기능 요구 사항<br/>
* 고객이 원하는 메뉴를 장바구니에 담아 주문할 수 있습니다. <br/>
* 고객은 주문시에 요청 사항이 있을 경우 메시지를 입력하여 전달 할 수 있습니다.
* 고객이 해당 주문을 취소할 수 있습니다. <br/>
* 고객이 주문 번호에 따른 주문이 완료가 되었는지 여부를 확인할 수 있습니다. <br/>
* 고객은 최근 완료 처리가 된 주문에 대해 상세 정보를 볼 수 있습니다. <br/>
<br/><br/>
* 관리자는 관리자 페이지에 접속할 수 있습니다.
* 관리자는 관리자 페이지에서 주문 번호에 따라 주문완료를 직접 처리 할 수 있습니다.
* 관리자는 대기 주문 목록과 완료 처리가 된 주문 목록을 조회할 수 있습니다.
* 관리자는 카테고리에 따른 상품 메뉴를 추가 혹은 삭제할 수 있습니다.


<br/><br/><br/>


## 기능 구현에 대한 기술적인 부분

### 1. List Collection 활용
<br/>

* 객체 타입의 다중배열이 아닌 List Collection을 활용하였습니다.<br/>
  * 다중배열을 사용하게 되면 index처리를 하게 되면서 가독성이 떨어지게 되므로 List를 선택했습니다.<br/>
  * 객체 타입의 다중 배열을 사용할 경우, 변수를 선언할 때 크기를 지정해야 하므로 확장성이 떨어진다는 단점이 있습니다.<br/>
  
```java
public class MenuContext {

	private Map<String, List<Menu>> menus;
	private Map<String, List<Item>> menuItems;
	private List<Item> cart;
	private double totalPrice;
	private int orderNumber;
	private List<Order> waitingOrders; // add - 대기 주문 상품
	private List<Order> completedOrders; // add - 완료된 주문 상품
	private String requestContent;
  
}  
```

* 장바구니, 대기 주문 목록, 완료 처리된 목록을 List 타입의 변수를 선언하였습니다.<br/><br/>
  * Item Class 타입의 객체를 장바구니 List에 담으면 Cart에 저장이 됩니다.<br/>
  * 장바구니에 있는 상품을 주문하게 되면 새로운 new Order 클래스 타입의 order 객체를 생성합니다.<br/> 
  * 생성된 order 객체의 멤버변수인 orderItems List에 장바구니의 요소들을 담습니다. <br/><br/>
    ```java
    	private static void setWaitingOrder(String request) {
		Order order = new Order();
		Date now = new Date();

		// List의 깊은 복사
		List<Item> it = new ArrayList<>();
		for(Item its : menuContext.getCart()){
			it.add(its);
		}

		order.setOrderItems(it);
		order.setTotalPrice(menuContext.getTotalPrice());
		order.setRequestContent(request); //요청 사항
		order.setOrderDate(now);
		order.generateOrderCnt();
		order.setOrderNum(menuContext.getOrderNumber());
		menuContext.addToWaitingOrder(order);
	}// setWaitingOrder() of the end
    ```
  * ***이때 깊은 복사(Deep Copy)를 하여 장바구니의 요소들의 값이 변해도 orderItems 내용은 변하지 않도록 합니다.*** <br/>
  * waitingOrders에 생성된 order 객체를 요소로 add 시켜 해당 주문을 대기 중인 주문 목록에 추가합니다. <br/>
  
  <br/><br/>
  
### 2. Map Collection 활용
```java
public class MenuContext {
	private Map<String, List<Menu>> menus;
	private Map<String, List<Item>> menuItems;
  
  --중략--
  
  	public MenuContext() {
		  menus = new HashMap<>();
	  	menuItems = new HashMap<>();
	  	cart = new ArrayList<>();
	  	totalPrice = 0.0;
	  	orderNumber = 0;
	  	waitingOrders = new ArrayList<>();
	  	completedOrders = new ArrayList<>();

  		initializeMenuItems();
	}
}  
```
  ```java
  	private void initializeMenuItems() {
		List<Menu> mainMenus = new ArrayList<>();
		mainMenus.add(new Menu("Burgers", "앵거스 비프 통살을 다져만든 버거"));
		mainMenus.add(new Menu("Forzen Custard", "매장에서 신선하게 만드는 아이스크림"));
		mainMenus.add(new Menu("Drinks", "매장에서 직접 만드는 음료"));
		mainMenus.add(new Menu("Beer", "뉴욕 브루클린 브루어리에서 양조한 맥주"));

		List<Menu> orderMenus = new ArrayList<>();
		orderMenus.add(new Menu("Order", "장바구니를 확인 후 주문합니다."));
		orderMenus.add(new Menu("Cancel", "진행중인 주문을 취소합니다."));

		menus.put("Main", mainMenus);
		menus.put("Order", orderMenus);

		List<Item> burgersMenus = new ArrayList<>();
		burgersMenus.add(new Item("ShackBurger", 6.9, "토마토, 양상추, 쉑소스가 토핑된 치즈버거"));
		burgersMenus.add(new Item("SmokeShack", 8.9, "베이컨, 체리 페퍼에 쉑소스가 토핑된 치즈버거"));
		burgersMenus.add(new Item("Shroom Burger", 9.4, "몬스터 치즈와 체다 치즈로 속을 채운 베지테리안 버거"));
		burgersMenus.add(new Item("Cheeseburger", 6.9, "포테이토 번과 비프패티, 치즈가 토핑된 치즈버거"));
		burgersMenus.add(new Item("Hamburger", 5.4, "비프패티를 기반으로 야채가 들어간 기본버거"));

		List<Item> frozenCustardMenu = new ArrayList<>();
		frozenCustardMenu.add(new Item("Vanilla Ice cream", 1.4, "It's a basic"));
		frozenCustardMenu.add(new Item("Chocolate peanuts butter Ice cream", 1.0, "Delish thing"));

		List<Item> drinksMenu = new ArrayList<>();
		drinksMenu.add(new Item("Coke", 1.5, "Coca Cola"));
		drinksMenu.add(new Item("Canada Dry", 1.5, "It's Ginger Ale"));

		List<Item> beerMenu = new ArrayList<>();
		beerMenu.add(new Item("Cass", 4.0, "Origin beer in Korea"));
		beerMenu.add(new Item("Draft Beer", 4.0, "Every like it"));

		menuItems.put("Burgers", burgersMenus);
		menuItems.put("Frozen Custard", frozenCustardMenu);
		menuItems.put("Drinks", drinksMenu);
		menuItems.put("Beer", beerMenu);
	}  
  ```
* 클래스의 생성자 메서드를 호출할 경우, initializeMenuItems() 메서드를 수행합니다. <br/>
  * 해당 메서드 내에서 메뉴와 상품 등에 대한 정보를 입력하고 Map형태의 변수에 값을 입력했습니다. <br/>
  * ShakeShackBurgerApplication 클래스 메인 메서드에서 MenuContext 객체를 인스턴스화 하여 displayMainMenu() 메서드를 통해 모든 기능을 실행시킵니다. <br/>
<br/>

  ```java
  	private static void displayBurgersMenu() {
		System.out.println("SHAKESHACK BURGER 에 오신걸 환영합니다.");
		System.out.println("아래 상품메뉴판을 보시고 상품을 골라 입력해주세요.\n");

		System.out.println("[ Burgers MENU ]");
		List<Item> burgerItems = menuContext.getMenuItems("Burgers");
		printMenuItems(burgerItems);

		handleMenuItemInput(burgerItems);
	}
  ```
  <br/>
  
  ***List<Item> burgerItems = menuContext.getMenuItems("Burgers");*** <br/>
  * Buregers라는 키값에 따른 List변수를 반환하여 burgerItems에 저장한 후, 출력합니다.<br/>
  * Map collection의 특성인 키 값에 따른 데이터를 가져오는 기능을 활용하였습니다. <br/>
  <br/>


<br/><br/>

## 예외 처리 <br/>

### 1. Scanner 예외 처리 <br/>
Class : ShakeShackBurgerApplication <br/>
Method : handleMainMenuInput() , displayAdminMenu() , printCompletedOrder() , confirmCompleteOrder() , deleteItem() <br/>
```java
	private static void handleMainMenuInput() {
		Scanner scanner = new Scanner(System.in);
		int input = 0;
		try{
			input = scanner.nextInt();
		}catch (InputMismatchException e){
			System.out.println("잘못된 요청입니다. 다시 입력해주세요");
			displayMainMenu();
		}
```
```java
	private static void displayAdminMenu() {
		Scanner scanner = new Scanner(System.in);
		System.out.println("이곳은 관리자 페이지입니다.");

		System.out.println("1. 대기주문 목록");
		System.out.println("2. 완료주문 목록");
		System.out.println("3. 상품 생성");
		System.out.println("4. 상품 삭제");
		System.out.println("5. 메인 페이지");
		System.out.print("항목을 선택하세요: ");

		int input = 0;
    
		try{
			input = scanner.nextInt();
		}catch (Exception e){
			System.out.println("잘못된 요청입니다. 다시 입력해주세요.");
		}
    
		switch (input) {
    
			--중략-- 
      
			default:
				System.out.println("잘못된 입력입니다. 다시 입력해주세요.");
				displayAdminMenu(); // 재귀 호출
				break;
		}// switch() of the end
	}// displayAdminMenu() of the end
```    
```java
	private static void printCompletedOrder() {
		Scanner scanner = new Scanner(System.in);

		-- 중략 --
    
		System.out.println("1. 메뉴판");
		int input = 0;
		try{
			input = scanner.nextInt();
		}catch (Exception e){
			System.out.println("잘못된 요청입니다. 다시 입력하세요!");
			printCompletedOrder();
		}
		if(input==1){
			System.out.println("========================================");
			displayMainMenu();
		}else {
			System.out.println("잘못된 입력입니다.");
			System.out.println("========================================");
			printCompletedOrder();
		}// if~else() of the end
	}// printCompletedOrder() of the end
```
```java
	private static void confirmCompleteOrder(Order selectedOrder) {
		Scanner scanner = new Scanner(System.in);
		System.out.println("아래 주문을 완료 처리 하시겠습니까?\n");

		printOrder(selectedOrder);

		System.out.println("1. 완료      2. 메뉴판");
		int confirm = scanner.nextInt();
		if(confirm==1){
			//menuContext.addToCompleteOrder(selectedOrder);
			setCompleteOrder(selectedOrder); // 주문 완료 리스트에 넣기
			resetWaitingOrder(selectedOrder); // 대기 주문 리스트에서 빼기
			System.out.println("해당 주문을 완료 처리 하였습니다.");
			System.out.println("========================================");
			displayMainMenu();
		}else if(confirm==2){
			System.out.println("========================================");
			displayMainMenu();
		}else {
			System.out.println("잘못된 입력입니다.");
			confirmCompleteOrder(selectedOrder);
		}// if~else() of the end
	}// completeOrder() of the end
```
```java
	private static void deleteItem() {
		Scanner scanner = new Scanner(System.in);

		System.out.println("삭제할 상품 정보를 입력해주세요.");
		System.out.print("메뉴: ");
		int menu = 0;
		try{
			menu = scanner.nextInt();
		}catch (Exception e){
			System.out.println("잘못된 요청입니다. 다시 입력하세요.");
			deleteItem();
		}
```
<br/>


* 메소드 내에서 Scanner를 통해 입력값을 int형으로 받고 있습니다. <br/>
* 만약, 입력 받은 값이 int형 변수인 input에 저장이 원활히 되지 않을 경우 <br/>
**"잘못된 요청입니다. 다시 입력해주세요."** 를 출력하도록 예외처리를 해두었습니다. <br/> <br/>
* 입력받은 값이 int형은 맞으나, swith ~ case문 혹은 if ~ else문 내부 조건따라 다시 입력받거나 메인페이지로 이동합니다.<br/>
* 다시 입력을 받기 위해 switch ~ case문의 default의 코드가 실행됨으로써 **재귀 호출**을 수행합니다. <br/><br/>

#### ***Result :*** 
![image](https://github.com/Chaeyounglim/Kiosk/assets/55676554/276e5838-e7d0-4a9d-9e85-afe3bab1f33c)
<br/><br/><br/>


### 2. 빈 List 출력 시에 예외 처리 <br/>
Class : ShakeShackBurgerApplication <br/>
Method : displayWaitingOrder() , printCompletedOrder() , RecentOrder() <br/>
```java
	private static void displayWaitingOrder() {
		Scanner scanner = new Scanner(System.in);
		System.out.println("========================================");
		System.out.println("대기 중인 주문 목록입니다.\n");

		System.out.println("[ 대기 주문 목록 ]");
		List<Order> waitingOrders = menuContext.getWaitingOrders();
		if(waitingOrders.isEmpty()){
			System.out.println("대기 중인 주문이 없습니다.");
			System.out.println("========================================");
			displayMainMenu();
		}else {
			printOrders(waitingOrders);

			System.out.println("완료할 주문 대기 번호를 입력해주세요.");
			handleWaitingOrders(waitingOrders); // 주문 완료 처리할 메서드
		}// if~else() of the end
	}// displayWaitingOrder() of the end
```
```java
	private static void printCompletedOrder() {
		Scanner scanner = new Scanner(System.in);

		System.out.println("========================================");
		System.out.println("처리 완료된 주문 목록입니다.\n");

		System.out.println("[ 완료 주문 목록 ]");
		if(!menuContext.getCompletedOrders().isEmpty()) {
			printOrders(menuContext.getCompletedOrders());
		}else{
			System.out.println("완료된 주문이 없습니다.");
		}// if~else() of the end
		System.out.println("========================================");
    -- 이하 생략 --
```
```java
	private static void RecentOrder(){
		System.out.println("========================================");
		System.out.println("대기 중인 주문 목록입니다.\n");

		System.out.println("[ 대기 주문 목록 ]");
		List<Order> waitingOrders = menuContext.getWaitingOrders();
		if(waitingOrders.isEmpty()){
			System.out.println("대기 중인 주문이 없습니다.");
		}else {
			printOrders(waitingOrders);
		}// if~else() of the end
		System.out.println("========================================");

		System.out.println("처리가 완료된 주문 목록입니다.\n");
		System.out.println("[ 최근 주문 완료 목록 ]");
		try {
			printRecentOrders(menuContext.getCompletedOrders());
			System.out.println("완료된 주문이 없습니다.");
			System.out.println("========================================\n");
		}catch (Exception e){
			System.out.println("잘못된 요청입니다.");
			displayMainMenu();
		}
		displayMainMenu();
	}
```
<br/>


* 메소드 내에서 List 타입 변수의 내용을 출력할 때, List가 비어있을 경우 <br/>
  **대기 중인 주문이 없습니다** 혹은 **완료된 주문이 없습니다.** 를 출력하도록 예외처리를 해두었습니다<br/>
* 조회할 주문 목록이 없기 때문에 다시 메인 페이지로 이동하도록 displayMainMenu()를 수행합니다. <br/><br/>

#### ***Result :*** 
![image](https://github.com/Chaeyounglim/Kiosk/assets/55676554/ca9feb77-8c58-4056-9630-5b2bd2b8ed26)
<br/><br/><br/>


### 3. List 조회 시에 예외 처리 <br/>
Class : ShakeShackBurgerApplication <br/>
Method : deleteItem() , handleWaitingOrders() <br/>
```java
	private static void deleteItem() {
		Scanner scanner = new Scanner(System.in);
    
      -- 중략 --
		System.out.print("이름: ");
		String name = scanner.nextLine();

		int menuSize = menuContext.getMenus("Main").size();

		if (menu >= 1 && menu <= menuSize) {
			boolean isMenuExist = findDeleteMenu(menu, name);
			if (isMenuExist) {
				System.out.println("상품이 삭제되었습니다.\n");
				displayMainMenu();
			}
			else {
				System.out.println("해당 상품이 존재하지 않습니다.\n");
				int wantResult = wantToMainOrAgain();
				if(wantResult==2) {
					deleteItem();
				}else {
					displayMainMenu();
				}// inner1 if~else() of the end
			}// inner2 if~else() of the end
		} else {
			System.out.println("잘못된 메뉴입니다.");
			deleteItem();
		}// outer if~else() of the end
	}// deleteItem() of the end
```
```java
	private static void handleWaitingOrders(List<Order> orders) {
		Scanner scanner = new Scanner(System.in);
		int input = scanner.nextInt(); // 선택한 주문 번호
		Order selectedOrder = new Order(); // 선택한 주문
		boolean hasSelectedOrder = false; // 입력한 대기 번호에 맞는 주문이 있는지 여부

		for (Order order : orders) {
			if (order.getOrderNum() == input) {
				selectedOrder = order;
				hasSelectedOrder = true;
			}// if() of the end
		}// for() of the end

		if (hasSelectedOrder) { // 입력받은 번호에 맞는 주문이 있을 경우
			confirmCompleteOrder(selectedOrder);
		} else { // 입력받은 번호에 맞는 주문이 없을 경우
			System.out.println("해당 번호에 맞는 주문이 없습니다.");
			System.out.println("다시 입력해주세요.");
			handleWaitingOrders(orders);
		}// if~else() of the end
	}// handleWaitingOrders() of the end
```
<br/>


* 메소드 내에서 List 타입 변수에 해당 조건에 맞는 객체가 없을 경우 <br/>
  **해당 조건에 맞는 주문이 없습니다. 다시 입력해주세요**를 출력하도록 예외처리를 해두었습니다.<br/>
* 필요한 객체가 없기 때문에 다시 입력받도록 재귀호출을 수행합니다. <br/><br/>

#### ***Result :*** 
Method : handleWaitingOrders() <br/>
![image](https://github.com/Chaeyounglim/Kiosk/assets/55676554/d2424dfb-f9cb-4483-a400-46c9d3f2fcd0)<br/><br/>
Method : deleteItem()<br/>
![image](https://github.com/Chaeyounglim/Kiosk/assets/55676554/7714088d-b213-422e-9cf2-0dc111b46d83)<br/>
<br/><br/><br/>


<br/><br/>

## 환경설정<br/>
Language : Java
IDLE : IntelliJ community<br/>
JDK : openjdk version 17.0.7 <br/>
SDK : corretto-17 Amazon Corretto version 17.0.6 <br/>
<br/><br/>



