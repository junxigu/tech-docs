## Java的类型装载

Java有多种类装载器，其中一个虚拟机内置的叫 启动类装载器，用户自定义的类装载器都需要继承ClassLoader

有两种方式可以触发类型的装载，一种是使用forName、classloader等来装载类型，一种是程序使用了新的类型时虚拟机解析符号引用前装载类型

无论哪种方式触发类型装载，都需要装载器进行装载；当一个装载器装载类型时使用 双亲委派模型，也就是本装载器 装载类之前先用parent装载器来装载；除了启动类装载器外，每个装载器都有一个parent装载器，这样就组成了一个装载器链；所以每个类装载器得到装载请求时可能是通过其他装载器装载的；

真正装载类型的装载器被称为 定义类型装载器，装载器链上从 定义类型装载器 到 开始接受装载请求的装载器 都被称为 初始类型装载器

每个被装载的类型都有一个指向其 定义类型装载器的引用；而每个装载器都会保存一个类型列表，列表里存放的每个类型 都是该装载器作为其初始类型装载器的类型

一个类型的装载流程：

1. 当一个装载器接收到装载请求时，首先检查其类型列表里是否已经存在该类型，有则返回该类型
2. 否则委托parent装载器装载类型，这个委托流程会一直把请求传递到 启动类型装载器
3. 若parent装载器没办法装载类型(它会抛异常)，则由本装载器装载该类型，若装载不了则抛异常
4. 若装载成功，则新类型会有一个指向本装载器的引用，并且装载器链上从本装载开始到开始受到装载请求的装载器都会在其类型列表里增加一个新类型
5. 当一个类型装载了以后，虚拟机会检查其类型信息并使用本类型的 定义类型装载器 装载其父类型和所实现的接口类型(自定义的类型装载器在调用defineClass的时候进行)

**装载约束**

当两个由不同 定义类型装载器 装载的对象引用同一个限定名(包名)的类型时，在这两个定义类型装载器加载该类型的过程中，该类型必须由同一个 装载器加载

**自定义装载器例子**

覆盖findClass，在此方法里获取class文件格式的二进制流并使用defineClass创建 Class实例并返回

```

class MyClassLoader extends ClassLoader {

	private String basePath;

	MyClassLoader(String basePath) {
		this.basePath = basePath;
	}

	MyClassLoader(ClassLoader parent, String basePath) {
		super(parent);
		this.basePath = basePath;
	}

	@Override
	protected Class<?> findClass(String className) throws ClassNotFoundException {
		byte[] classData = getTypeFromBasePath(className);

		// Try to load the class from base path directory
		if (classData == null) {
			throw new ClassNotFoundException();
		}
		
		// Parse the class binary data to create a Class instance 
		return defineClass(className, classData, 0, classData.length);
	}

	private byte[] getTypeFromBasePath(String typeName) {
		String fileName = basePath + File.separator + typeName.replace(".", File.pathSeparator) + ".class";

		try {
			BufferedInputStream bis = new BufferedInputStream(new FileInputStream(fileName));
			ByteArrayOutputStream out = new ByteArrayOutputStream();

			int c = bis.read();
			while (c != -1) {
				out.write(c);
				c = bis.read();
			}
			return out.toByteArray();
		} catch (IOException e) {
			return null;
		}
	}
}


```