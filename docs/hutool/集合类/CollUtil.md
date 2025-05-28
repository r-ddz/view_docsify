## isEmpty

```java
    /**
     * 集合是否为空
     *
     * @param collection 集合
     * @return 是否为空
     */
    public static boolean isEmpty(Collection<?> collection) {
        return collection == null || collection.isEmpty();
    }
```

## size

```java
	/**
	 * 获取Collection或者iterator的大小，此方法可以处理的对象类型如下：
	 * <ul>
	 * <li>Collection - the collection size
	 * <li>Map - the map size
	 * <li>Array - the array size
	 * <li>Iterator - the number of elements remaining in the iterator
	 * <li>Enumeration - the number of elements remaining in the enumeration
	 * </ul>
	 *
	 * @param object 可以为空的对象
	 * @return 如果object为空则返回0
	 * @throws IllegalArgumentException 参数object不是Collection或者iterator
	 * @since 5.5.0
	 */
	public static int size(final Object object) {
		if (object == null) {
			return 0;
		}

		int total = 0;
		if (object instanceof Map<?, ?>) {
			total = ((Map<?, ?>) object).size();
		} else if (object instanceof Collection<?>) {
			total = ((Collection<?>) object).size();
		} else if (object instanceof Iterable<?>) {
			total = IterUtil.size((Iterable<?>) object);
		} else if (object instanceof Iterator<?>) {
			total = IterUtil.size((Iterator<?>) object);
		} else if (object instanceof Enumeration<?>) {
			final Enumeration<?> it = (Enumeration<?>) object;
			while (it.hasMoreElements()) {
				total++;
				it.nextElement();
			}
		} else if (ArrayUtil.isArray(object)) {
			total = ArrayUtil.length(object);
		} else {
			throw new IllegalArgumentException("Unsupported object type: " + object.getClass().getName());
		}
		return total;
	}
```

