# �ҵ�FilterChinaʵ����

chain.doFilter(request, response);

�� Ctrl+T �ҵ�ʵ�����б����ž��� `org.apache.catalina.core.ApplicationFilterChain` ���¸��ϵ�һ���Ծ�֪���ˡ�

# ��ȡFilters�б�

��ǿת��Ȼ��ʹ�÷���( `getDeclaredField` + `setAccessible`)��ȡ˽�б��� filters�� n��

ע�⣺filtersĬ����10���ȣ����治һ�����ˣ�n��������������

```Java
@SneakyThrows
private void printAllFilters(FilterChain chain) {
	ApplicationFilterChain filterChain = (ApplicationFilterChain) chain;

	// ��ȡ˽�б��� filters
	ApplicationFilterConfig[] filterConfigs = (ApplicationFilterConfig[]) readField(
			filterChain, "filters");
	int filterSize = (int) readField(filterChain, "n");

	System.out.println("\n\nprintAllFilters(), size=" + filterSize);

	for (int i = 0; i < filterSize; i++) {
		System.out.println("printAllFilters()" + filterConfigs[i].getFilterClass());
	}

	System.out.println("\n\n");
}

private Object readField(Object obj, String name)
		throws NoSuchFieldException, IllegalAccessException {
	Field field = obj.getClass().getDeclaredField(name);
	field.setAccessible(true);

	return field.get(obj);
}
```

�����ͼ��


```
printAllFilters(), size=7
printAllFilters()org.springframework.boot.web.filter.OrderedCharacterEncodingFilter
printAllFilters()org.springframework.boot.web.filter.OrderedHiddenHttpMethodFilter
printAllFilters()org.springframework.boot.web.filter.OrderedHttpPutFormContentFilter
printAllFilters()org.springframework.boot.web.filter.OrderedRequestContextFilter
printAllFilters()org.springframework.boot.web.servlet.DelegatingFilterProxyRegistrationBean$1
printAllFilters()cn.xiaowenjie.common.filters.UserFilter
printAllFilters()org.apache.tomcat.websocket.server.WsFilter
```

# �����ȡ���ԣ����÷���

```Java
private Object invokeMethod(Object obj, String methodName)
		throws IllegalAccessException, IllegalArgumentException,
		InvocationTargetException, NoSuchMethodException, SecurityException {
	Method method = obj.getClass().getDeclaredMethod(methodName, null);
	method.setAccessible(true);

	return method.invoke(obj, null);
}

private Object readField(Class<?> cls, Object obj, String name)
		throws NoSuchFieldException, IllegalAccessException {
	Field field = cls.getDeclaredField(name);
	field.setAccessible(true);

	return field.get(obj);
}

private Object readField(Object obj, String name)
		throws NoSuchFieldException, IllegalAccessException {
	return readField(obj.getClass(), obj, name);
}
```


����� `volatile` ���͵ģ������� `obj.getClass()`�������� ���͵� `.class`

|That is known as a class literal. The expression results in a Class object for the given type. Calling  getClass() on a Class instance will return the Class instance for Class, not for MyClass.
|
|(All this assumes that the field mInstance is declared in MyClass.)

