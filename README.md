## Exam Rank 06

- [Fonksiyon Açıklamaları](#fonksiyon-açıklamaları)
	- [`write`](#write)
	- [`exit`](#exit)
	- [`strlen`](#strlen)
	- [`free`](#free)
	- [`atoi`](#atoi)
	- [`socket`](#socket)
	- [`fcntl`](#fcntl)
		- [`F_SETFL` Sabiti](#f_setfl-sabiti)
	- [`fd_set`](#fd_set)
	- [`fd_set` Yapısı Makroları](#fd_set-yapısı-makroları)
		- [FD\_SET](#fd_set-1)
		- [FD\_CLR](#fd_clr)
		- [FD\_ISSET](#fd_isset)
		- [FD\_ZERO](#fd_zero)
		- [Örnek Kullanım: (select ile birlikte)](#örnek-kullanım-select-ile-birlikte)
	- [`bzero`](#bzero)
	- [`htonl`](#htonl)
	- [`htons`](#htons)
	- [`bind`](#bind)
	- [`listen`](#listen)
	- [`select`](#select)
	- [`accept`](#accept)
	- [`sprintf`](#sprintf)
	- [`send`](#send)
	- [`recv`](#recv)
	- [`close`](#close)
	- [`malloc`](#malloc)
	- [`calloc`](#calloc)
	- [`strcat`](#strcat)
	- [`strcpy`](#strcpy)

<br></br>

## Fonksiyon Açıklamaları

### `write`

- Tanım: Dosyalara, aygıtlara veya akışlara doğrudan veri yazmak için kullanılan bir sistem çağrısıdır (system call).
- Syntax:
	```c
	#include <unistd.h>
	ssize_t write(int fd, const void *buf, size_t count);
	```
- Parametreler:
	- fd: Dosya tanımlayıcısı (file descriptor). Yazma işleminin yapılacağı hedef.
		- 0 = standart giriş (stdin)
		- 1 = standart çıkış (stdout)
		- 2 = standart hata (stderr)
	- buf: Yazılacak verinin başladığı bellek adresi. Genellikle bir karakter dizisi (char *) olur.
	- count: Yazılacak byte sayısı. buf’daki kaç bayt verinin yazılacağı.

- Örnek:
	```c
	#include <unistd.h>
	#include <string.h>

	int main()
	{
		const char *message = "Merhaba, dünya!\n";
		ssize_t bytes_written = write(1, message, strlen(message)); // 1 -> stdout

		if (bytes_written == -1)
		{
			// Hata işlemi
			write(2, "Yazma hatası!\n", 14); // 2 -> stderr
			return 1;
		}

		return 0;
	}
	```

<br></br>

### `exit`

- Tanım: Programın hemen sona ermesini sağlar.
- Syntax:
	```c
	#include <stdlib.h>
	void exit(int status);
	```
- Parametre:
	- status: Programın çıkış durumu (exit status). Genellikle 0 başarı, 0 dışı değerler hata anlamına gelir.
- Örnek:
	```c
	#include <stdlib.h>
	#include <stdio.h>

	int main()
	{
		printf("Program başarıyla çalıştı.\n");
		exit(0); // Başarı ile çıkış

		printf("Bu satır çalışmaz.\n");
		return 0; // Bu satır da çalışmaz
	}
	```

<br></br>

### `strlen`

- Tanım: Bir C stringinin (null-terminated char dizisi) uzunluğunu (null karakter hariç) döner.
- Syntax:
	```c
	#include <string.h>
	size_t strlen(const char *str);
	```
- Parametreler:
	- str: Uzunluğu bulunacak string.
	- return değeri: Stringdeki karakter sayısı (null karakter '\0' sayılmaz).
- Örnek:
	```c
	#include <stdio.h>
	#include <string.h>

	int main()
	{
		const char *s = "Merhaba";
		printf("Uzunluk: %zu\n", strlen(s));  // Çıktı: Uzunluk: 7
		return 0;
	}
	```

<br></br>

### `free`

- Tanım: Dinamik olarak malloc, calloc veya realloc ile ayrılmış belleği serbest bırakır. Bellek sızıntısını önlemek için kullanılır.
- Syntax:
	```c
	#include <stdlib.h>
	void free(void *ptr);
	```

- Parametre:
	- ptr: Serbest bırakılacak belleğe işaret eden gösterici.
		- NULL ise hiçbir işlem yapılmaz.
		- Aynı bellek iki kez free edilmemelidir.
- Örnek:
	```c
	#include <stdio.h>
	#include <stdlib.h>

	int main()
	{
		int *p = (int *)malloc(5 * sizeof(int));
		if (p == NULL)
		{
			printf("Bellek ayrilamadi!\n");
			return 1;
		}

		for (int i = 0; i < 5; i++)
			p[i] = i;

		for (int i = 0; i < 5; i++)
			printf("%d ", p[i]);

		free(p);  // Belleği serbest bırak
		return 0;
	}
	```

<br></br>

### `atoi`

- Tanım: Bir karakter dizisini (string) tamsayıya (int) çeviren bir fonksiyondur.
- Syntax:
	```c
	#include <stdlib.h>
	int atoi(const char *str);
	```
- Parametreler:
	- str: Sayıya çevrilmek istenen string (null ile sonlanan C dizisi).
	- Return değeri: Çevrilen tamsayı değeri. Eğer geçerli bir sayı içermezse 0 döner.
- Örnek:
	```c
	#include <stdio.h>
	#include <stdlib.h>

	int main()
	{
		const char *sayi = "456";
		int n = atoi(sayi);
		printf("Sayi: %d\n", n);  // Çıktı: Sayi: 456
		return 0;
	}
	```

<br></br>

### `socket`

- Tanım: socket() fonksiyonu, ağ (network) üzerinden iletişim kurmak için bir socket (soket) oluşturur. TCP/IP bağlantıları gibi ağ iletişimlerinin başlangıç noktasıdır.
- Syntax:
	```c
	#include <sys/types.h>
	#include <sys/socket.h>

	int socket(int domain, int type, int protocol);
	```
- Parametreler:
	- domain: Hangi adres ailesi kullanılacak?
		- AF_INET → IPv4
		- AF_INET6 → IPv6
		- AF_UNIX → Unix domain socket
	- type: Soket türü (iletişim tipi)
		- SOCK_STREAM → TCP (bağlantı tabanlı)
		- SOCK_DGRAM → UDP (bağlantısız)
	- protocol: Protokol türü. Genellikle 0 yazılır; sistem doğru protokolü kendisi seçer (örneğin TCP için IPPROTO_TCP).
	- Return değeri:
		- Başarılıysa: Pozitif bir socket tanımlayıcısı (file descriptor) döner.
		- Hatalıysa: -1 döner ve errno ile hata açıklanabilir.
- Örnek:
	```c
	#include <stdio.h>
	#include <stdlib.h>
	#include <sys/socket.h>
	#include <netinet/in.h>
	#include <unistd.h>

	int main()
	{
		int sockfd = socket(AF_INET, SOCK_STREAM, 0);
		if (sockfd == -1)
		{
			perror("socket");
			exit(1);
		}

		printf("Socket oluşturuldu: %d\n", sockfd);
		close(sockfd);  // Soketi kapat
		return 0;
	}
	```
- Notlar:
	- socket() sadece soketi oluşturur, iletişim kurmak için bind(), listen(), accept() gibi fonksiyonlar da gerekir.
	- Her socket() çağrısı sistem kaynaklarını kullanır; iş bitince close() ile kapatılmalıdır.

<br></br>

### `fcntl`

- Tanım:
fcntl (file control), açık bir dosya tanımlayıcısı (file descriptor) üzerinde çeşitli kontrol ve ayarlamalar yapmak için kullanılan çok amaçlı bir POSIX sistem çağrısıdır. Genellikle non-blocking ayarlamak, dosya kilitlemek, bayrakları değiştirmek gibi işler için kullanılır.
- Syntax:
	```c
	#include <fcntl.h>
	#include <unistd.h>

	int fcntl(int fd, int cmd, ... /* arg */ );
	```
- Parametreler:
	- fd: İşlem yapılacak açık dosya tanımlayıcısı (örneğin soket, dosya, pipe vb.).
	- cmd: Yapılacak işlem türünü belirleyen komut. Bazı yaygın komutlar:
		- F_GETFL → Dosya tanımlayıcısının bayraklarını al
		- F_SETFL → Dosya tanımlayıcısının bayraklarını ayarla
		- F_GETFD, F_SETFD → Dosya tanımlayıcısının durum bayrakları (close-on-exec gibi)
	- arg: Bazı komutlar ek bir argüman gerektirir (örneğin F_SETFL komutu için yeni bayrak değeri).
	- Return değeri:
		- Başarıyla tamamlanırsa pozitif bir değer (genellikle bir bayrak veya onay).
		- Hata varsa -1 döner ve errno ayarlanır.
- Örnek (Soketi non-blocking yapmak):
	```c
	#include <stdio.h>
	#include <stdlib.h>
	#include <unistd.h>
	#include <fcntl.h>
	#include <sys/socket.h>

	int main()
	{
		int sockfd = socket(AF_INET, SOCK_STREAM, 0);
		if (sockfd == -1)
		{
			perror("socket");
			exit(1);
		}

		int flags = fcntl(sockfd, F_GETFL, 0);
		if (flags == -1)
		{
			perror("fcntl - get");
			close(sockfd);
			exit(1);
		}

		if (fcntl(sockfd, F_SETFL, flags | O_NONBLOCK) == -1)
		{
			perror("fcntl - set O_NONBLOCK");
			close(sockfd);
			exit(1);
		}

		printf("Soket artık non-blocking modda.\n");
		close(sockfd);
		return 0;
	}
	```

#### `F_SETFL` Sabiti

- Tanım: fcntl() fonksiyonu ile birlikte kullanılarak bir dosya tanımlayıcısının (örneğin soket, dosya) açıkken geçerli olan bayraklarını (flags) ayarlamak için kullanılır.
- Syntax:
	```c
	#include <fcntl.h>
	fcntl(int fd, F_SETFL, int new_flags);
	```
- Parametreler:
	- fd: İşlem yapılacak dosya tanımlayıcısı (örneğin soket).
	- F_SETFL: Bayrakları ayarlamak için kullanılan komuttur.
	- new_flags: Yeni bayraklar. Genellikle `O_NONBLOCK`, `O_APPEND` gibi makrolar kullanılır. Mevcut bayraklar üzerine ekleme yapmak için önce `F_GETFL` ile alınan mevcut bayraklara `| (bitwise OR)` ile ekleme yapılır.
- Örnek En Yaygın Kullanım (Non-blocking Ayarlamak):
	```c
	#include <fcntl.h>
	#include <unistd.h>
	#include <stdio.h>
	#include <stdlib.h>
	#include <sys/socket.h>

	int main()
	{
		int sockfd = socket(AF_INET, SOCK_STREAM, 0);
		if (sockfd == -1)
		{
			perror("socket");
			exit(1);
		}

		// Mevcut bayrakları al
		int flags = fcntl(sockfd, F_GETFL, 0);
		if (flags == -1)
		{
			perror("fcntl - F_GETFL");
			close(sockfd);
			exit(1);
		}

		// Non-blocking mod ekle
		if (fcntl(sockfd, F_SETFL, flags | O_NONBLOCK) == -1)
		{
			perror("fcntl - F_SETFL");
			close(sockfd);
			exit(1);
		}

		printf("Soket artık non-blocking modda!\n");
		close(sockfd);
		return 0;
	}
	```

<br></br>

### `fd_set`

- Tanım: fd_set, birden fazla dosya tanımlayıcısını (file descriptor) aynı anda izlemek için kullanılan bir küme (set) yapısıdır. Genellikle select() sistem çağrısı ile birlikte kullanılır. Örneğin birden fazla soketi aynı anda veri var mı diye kontrol etmek için kullanılır.
- Syntax:
	```c
	#include <sys/select.h>
	fd_set set;
	```
- fd_set bir tiptir, FD_ZERO, FD_SET, FD_CLR, FD_ISSET makroları ile birlikte kullanılır.
- Nasıl Kullanılır?
	| Makro              | Açıklama                                              |
	|--------------------|-------------------------------------------------------|
	| FD_ZERO(&set)      | Tüm dosya tanımlayıcılarını kümeden siler (sıfırlar). |
	| FD_SET(fd, &set)   | Belirtilen fd’yi kümeye ekler.                        |
	| FD_CLR(fd, &set)   | fd’yi kümeden çıkarır.                                |
	| FD_ISSET(fd, &set) | fd kümede var mı diye kontrol eder (1 veya 0 döner).  |

- Örnek:
	```c
	#include <stdio.h>
	#include <stdlib.h>
	#include <unistd.h>
	#include <sys/select.h>

	int main()
	{
		fd_set read_fds;
		int fd = 0; // standart giriş (klavye)

		FD_ZERO(&read_fds);      // Küme temizlenir
		FD_SET(fd, &read_fds);   // stdin kümeye eklenir

		printf("Girdi bekleniyor...\n");

		if (select(fd + 1, &read_fds, NULL, NULL, NULL) > 0) {
			if (FD_ISSET(fd, &read_fds)) {
				char buf[100];
				read(fd, buf, sizeof(buf));
				printf("Girdi alındı: %s", buf);
			}
		}

		return 0;
	}
	```

<br></br>

### `fd_set` Yapısı Makroları

Bu makrolar fd_set yapısı ile birlikte çalışır. fd_set, dosya tanımlayıcı kümelerini (örneğin soketleri) temsil eder ve select() fonksiyonu ile birlikte kullanılır.

#### FD_SET
- Tanım: Belirtilen dosya tanımlayıcısını (fd) fd_set kümesine ekler.
- Syntax:
	```c
	void FD_SET(int fd, fd_set *set);
	```
- Parametreler:
	- fd: Eklenecek dosya tanımlayıcısı.
	- set: Hedef fd_set kümesi.

<br>

#### FD_CLR
- Tanım: Belirtilen fd’yi fd_set kümesinden çıkarır.
- Syntax:
	```c
	void FD_CLR(int fd, fd_set *set);
	```
<br>

#### FD_ISSET
- Tanım: Belirtilen fd’nin fd_set içinde olup olmadığını kontrol eder.
- Syntax:
	```c
	int FD_ISSET(int fd, const fd_set *set);
	```
- Return Değerleri:
	- 1: Varsa
	- 0: Yoksa

<br>

#### FD_ZERO
- Tanım: fd_set içindeki tüm bitleri 0 yapar (yani boş set oluşturur).
- Syntax:
	```c
	void FD_ZERO(fd_set *set);
	```

<br>

#### Örnek Kullanım: (select ile birlikte)
```c
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>
#include <sys/socket.h>
#include <netinet/in.h>
#include <string.h>
#include <sys/select.h>

int main()
{
	int server_fd, client_fd, max_fd;
	struct sockaddr_in addr;
	socklen_t addrlen = sizeof(addr);

	// Soket oluştur
	server_fd = socket(AF_INET, SOCK_STREAM, 0);
	if (server_fd < 0)
	{
		perror("socket");
		exit(1);
	}

	addr.sin_family = AF_INET;
	addr.sin_addr.s_addr = INADDR_ANY;
	addr.sin_port = htons(8080);

	bind(server_fd, (struct sockaddr *)&addr, sizeof(addr));
	listen(server_fd, 5);

	fd_set read_fds;
	FD_ZERO(&read_fds);
	FD_SET(server_fd, &read_fds);
	max_fd = server_fd;

	printf("Bağlantı bekleniyor...\n");

	while (1)
	{
		fd_set tmp_fds = read_fds;

		if (select(max_fd + 1, &tmp_fds, NULL, NULL, NULL) < 0)
		{
			perror("select");
			exit(1);
		}

		// Sunucu soketi hazırsa yeni bağlantı kabul et
		if (FD_ISSET(server_fd, &tmp_fds))
		{
			client_fd = accept(server_fd, (struct sockaddr *)&addr, &addrlen);
			if (client_fd > 0)
			{
				printf("Yeni bağlantı: %d\n", client_fd);
				FD_SET(client_fd, &read_fds);
				if (client_fd > max_fd)
					max_fd = client_fd;
			}
		}

		// Diğer tüm soketlerde veri var mı kontrol et
		for (int i = 0; i <= max_fd; i++)
		{
			if (i != server_fd && FD_ISSET(i, &tmp_fds))
			{
				char buf[1024];
				int bytes = recv(i, buf, sizeof(buf) - 1, 0);
				if (bytes <= 0)
				{
					close(i);
					FD_CLR(i, &read_fds);
					printf("Bağlantı kapandı: %d\n", i);
				}
				else
				{
					buf[bytes] = '\0';
					printf("Veri [%d]: %s", i, buf);
				}
			}
		}
	}

	return 0;
}
```

<br></br>

### `bzero`

- Tanım: bzero, bir bellek bölgesini sıfırlamak (her byte’ını 0 yapmak) için kullanılan eski bir POSIX fonksiyonudur. Genellikle bir struct ya da buffer’ı temizlemek için kullanılır.
- Syntax:
	```c
	#include <strings.h>
	void bzero(void *s, size_t n);
	```
- Parametreler:
	- s: Sıfırlanacak bellek bölgesinin başlangıç adresi (genellikle bir struct veya dizi).
	- n: Kaç byte’lık alanın sıfırlanacağı.
- Örnek:
	```c
	#include <stdio.h>
	#include <strings.h>

	int main()
	{
		char buffer[10];
		bzero(buffer, sizeof(buffer));  // buffer'ın tüm elemanlarını 0 yapar

		for (int i = 0; i < 10; i++)
			printf("%d ", buffer[i]);  // Hepsi 0 yazdırır

		return 0;
	}
	```

<br></br>

### `htonl`

- Tanım: htonl (”host to network long”), bir unsigned int (32-bit = long olarak kabul edilir) değeri host byte sırasından (endianness), network byte sırasına (big-endian) çevirir. Ağ programlamasında IP adresleri ve portlar belirli bir byte sırası (big-endian) ile iletildiğinden bu dönüşüm gereklidir.
- Syntax:
	```c
	#include <arpa/inet.h>
	uint32_t htonl(uint32_t hostlong);
	```
- Parametreler:
	- hostlong: Dönüştürülecek 32-bit (genellikle unsigned int) uzunlukta sayı.
	- Return değeri: Network byte sırasına çevrilmiş hali.
- Örnek:
	```c
	#include <stdio.h>
	#include <arpa/inet.h>

	int main()
	{
		uint32_t host_ip = 2130706433;    // 127.0.0.1 in decimal form
		uint32_t net_ip = htonl(host_ip);

		printf("Host byte order: %u\n", host_ip);
		printf("Network byte order: %u\n", net_ip);

		return 0;
	}
	```

> 2130706433 = 127.0.0.1
>
> Host sistem little-endian ise, bu sayı ağ için byte sıralaması değiştirilmiş olur.

<br></br>

### `htons`

- Tanım: htons (”host to network short”), bir unsigned short (16-bit) değeri host byte sırasından (endianness), network byte sırasına (big-endian) çevirir. Genellikle port numaralarını ağ için uygun hale getirmek amacıyla kullanılır.
- Syntax:
	```c
	#include <arpa/inet.h>
	uint16_t htons(uint16_t hostshort);
	```
- Parametreler:
	- hostshort: Dönüştürülecek 16-bit kısa sayı (örneğin port numarası).
	- Return değeri: Network byte sırasına çevrilmiş hali.
- Örnek:
	```c
	#include <stdio.h>
	#include <arpa/inet.h>

	int main()
	{
		uint16_t port = 8080;
		uint16_t net_port = htons(port);

		printf("Host port: %u\n", port);
		printf("Network port: %u\n", net_port);

		return 0;
	}
	```

<br></br>

### `bind`

- Tanım: Bir soketi (socket) belirli bir IP adresine ve port numarasına bağlamak için kullanılır. Sunucu tarafında soketi dinlemeye (listen) başlamadan önce mutlaka çağrılır.
- Syntax:
	```c
	#include <sys/types.h>
	#include <sys/socket.h>
	int bind(int sockfd, const struct sockaddr *addr, socklen_t addrlen);
	```
- Parametreler:
	- sockfd: socket() ile oluşturulmuş soket dosya tanımlayıcısı.
	- addr: IP adresi ve port bilgilerini içeren sockaddr yapısının pointer’ı.
	- addrlen: addr yapısının boyutu (sizeof(struct sockaddr_in) gibi).
	- Return değeri:
		- Başarılıysa 0 döner.
		- Hatalıysa -1 döner ve errno hata kodunu ayarlar.
- Örnek:
	```c
	#include <stdio.h>
	#include <stdlib.h>
	#include <string.h>
	#include <unistd.h>
	#include <arpa/inet.h>
	#include <sys/socket.h>

	int main()
	{
		int sockfd;
		struct sockaddr_in servaddr;

		sockfd = socket(AF_INET, SOCK_STREAM, 0);
		if (sockfd < 0)
		{
			perror("socket");
			exit(1);
		}

		memset(&servaddr, 0, sizeof(servaddr));
		servaddr.sin_family = AF_INET;
		servaddr.sin_addr.s_addr = htonl(INADDR_ANY); // Her IP'den bağlantı kabul et
		servaddr.sin_port = htons(8080);   // 8080 portunu dinle

		if (bind(sockfd, (struct sockaddr *)&servaddr, sizeof(servaddr)) < 0)
		{
			perror("bind");
			close(sockfd);
			exit(1);
		}

		printf("Soket 8080 portuna bağlandı.\n");

		close(sockfd);
		return 0;
	}
	```

<br></br>

### `listen`

- Tanım: listen, bir TCP soketini pasif moda geçirir; yani soket artık gelen bağlantıları kabul etmeye (accept) hazır hale gelir. Genellikle bind’den hemen sonra çağrılır.
- Syntax:
	```c
	#include <sys/socket.h>
	int listen(int sockfd, int backlog);
	```
- Parametreler:
	- sockfd: socket() ile oluşturulmuş ve bind() ile bir IP/port’a bağlanmış soket tanımlayıcısı.
	- backlog: Aynı anda kuyruğa alınabilecek maksimum bekleyen bağlantı sayısı. (Sistem bu sayıya kadar gelen bağlantıları sıraya alır.)
	- Return değeri:
		- Başarılıysa: 0
		- Hata durumunda: -1 döner ve errno ayarlanır.
- Örnek:
	```c
	#include <stdio.h>
	#include <stdlib.h>
	#include <unistd.h>
	#include <string.h>
	#include <arpa/inet.h>
	#include <sys/socket.h>

	int main()
	{
		int sockfd;
		struct sockaddr_in servaddr;

		sockfd = socket(AF_INET, SOCK_STREAM, 0);
		if (sockfd < 0)
		{
			perror("socket");
			exit(1);
		}

		servaddr.sin_family = AF_INET;
		servaddr.sin_addr.s_addr = INADDR_ANY;
		servaddr.sin_port = htons(8080);
		memset(&(servaddr.sin_zero), 0, 8);

		if (bind(sockfd, (struct sockaddr *)&servaddr, sizeof(servaddr)) < 0)
		{
			perror("bind");
			exit(1);
		}

		if (listen(sockfd, 10) < 0)
		{
			perror("listen");
			exit(1);
		}

		printf("Sunucu dinliyor...\n");
		// Buradan sonra accept() çağrılabilir

		close(sockfd);
		return 0;
	}
	```

<br></br>

### `select`

- Tanım: Birden fazla dosya tanımlayıcısını (örneğin soketleri) aynı anda izlemek için kullanılır. Hangi soket veya dosyada veri okunabilir, yazılabilir ya da hata var diye bekler. Böylece çoklu I/O işlemi (I/O multiplexing) yapılabilir.
- Syntax:
	```c
	#include <sys/select.h>
	int select(int nfds,
			fd_set *readfds,
			fd_set *writefds,
			fd_set *exceptfds,
			struct timeval *timeout);
	```
- Parametreler:
	- nfds: İzlenecek en büyük dosya tanımlayıcısının değeri + 1 (örneğin en büyük fd 5 ise, nfds = 6).
	- readfds: Veri okunabilir (read) durumda olan fd’lerin kümesi.
	- writefds: Yazılabilir (write) durumda olan fd’lerin kümesi.
	- exceptfds: Hata durumları için izlenen fd’lerin kümesi.
	- timeout: Bekleme süresi (NULL ise süresiz bekler).
	- Return değeri:
		- Pozitif sayı: Hazır olan fd sayısı.
		- 0: Timeout süresi doldu, hiç fd hazır değil.
		- -1: Hata oluştu, errno ayarlanır.
- Örnek:
	```c
	#include <stdio.h>
	#include <stdlib.h>
	#include <unistd.h>
	#include <sys/select.h>

	int main()
	{
		fd_set readfds;
		struct timeval tv;
		int ret;

		FD_ZERO(&readfds);
		FD_SET(STDIN_FILENO, &readfds);  // Standart giriş (klavye)

		tv.tv_sec = 5;   // 5 saniye bekle
		tv.tv_usec = 0;

		printf("Bir şey yazın (5 saniye içinde):\n");

		ret = select(STDIN_FILENO + 1, &readfds, NULL, NULL, &tv);

		if (ret == -1)
		{
			perror("select");
			exit(1);
		}
		else if (ret == 0)
		{
			printf("Zaman aşımı! Girdi yapılmadı.\n");
		}
		else
		{
			if (FD_ISSET(STDIN_FILENO, &readfds))
			{
				char buf[100];
				read(STDIN_FILENO, buf, sizeof(buf));
				printf("Girdi alındı: %s", buf);
			}
		}

		return 0;
	}
	```

<br></br>

### `accept`

- Tanım: daha önce listen() ile dinleme moduna alınmış bir sokete gelen yeni bağlantı isteğini kabul eder ve bu bağlantı için yeni bir soket dosya tanımlayıcısı oluşturur.
- Syntax:
	```c
	#include <sys/socket.h>
	int accept(int sockfd, struct sockaddr *addr, socklen_t *addrlen);
	```
- Parametreler:
	- sockfd: socket() ile oluşturulup bind() ve listen() yapılmış dinleme soketi.
	- addr: Bağlanan istemcinin adres bilgisinin yazılacağı yapı. (İsteğe bağlı, NULL olabilir)
	- addrlen: addr yapısının boyutu girilir ve dönüşte bağlantı adresinin gerçek uzunluğu yazılır. (İsteğe bağlı, NULL olabilir)
	- Return değeri:
		- Başarılı olursa, bağlantı için yeni bir soket dosya tanımlayıcısı (int) döner.
		- Hata durumunda -1 döner ve errno hata kodunu ayarlar.
- Örnek:
	```c
	#include <stdio.h>
	#include <stdlib.h>
	#include <string.h>
	#include <unistd.h>
	#include <arpa/inet.h>
	#include <sys/socket.h>

	int main()
	{
		int listen_fd, client_fd;
		struct sockaddr_in servaddr, cliaddr;
		socklen_t clilen;

		listen_fd = socket(AF_INET, SOCK_STREAM, 0);

		memset(&servaddr, 0, sizeof(servaddr));
		servaddr.sin_family = AF_INET;
		servaddr.sin_addr.s_addr = htonl(INADDR_ANY);
		servaddr.sin_port = htons(8080);

		bind(listen_fd, (struct sockaddr*)&servaddr, sizeof(servaddr));
		listen(listen_fd, 5);

		clilen = sizeof(cliaddr);
		client_fd = accept(listen_fd, (struct sockaddr*)&cliaddr, &clilen);
		if (client_fd < 0) {
			perror("accept");
			exit(1);
		}

		printf("Yeni bağlantı kabul edildi!\n");

		close(client_fd);
		close(listen_fd);
		return 0;
	}
	```

<br></br>

### `sprintf`

- Tanım: sprintf, printf gibi çalışır ama ekrana yazdırmaz; oluşturulan formatlı stringi bir karakter dizisine (char array) yazar.
- Syntax:
	```c
	#include <stdio.h>
	int sprintf(char *str, const char *format, ...);
	```
- Parametreler:
	- str: Formatlanmış çıktının yazılacağı karakter dizisi (hedef).
	- format: Yazım biçimini belirten format stringi (örn. %d, %s, %f, vs.).
	- ...: Format stringine karşılık gelen değişkenler.
	- Return değeri:
		- Yazılan karakter sayısı (null karakter \0 hariç).
		- Hata durumunda negatif değer döner.
- Örnek:
	```c
	#include <stdio.h>

	int main()
	{
		char buffer[100];
		int age = 25;
		sprintf(buffer, "Benim yaşım %d", age);

		printf("%s\n", buffer);  // Çıktı: Benim yaşım 25
		return 0;
	}
	```

<br></br>

### `send`

- Tanım: send, bir TCP soketi üzerinden veri göndermek için kullanılır. Genellikle connect() ile bağlanılmış bir soket üzerinden veri yollamak için çağrılır.
- Syntax:
	```c
	#include <sys/types.h>
	#include <sys/socket.h>
	ssize_t send(int sockfd, const void *buf, size_t len, int flags);
	```
- Parametreler:
	- sockfd: Veri gönderilecek soket dosya tanımlayıcısı
	- buf: Gönderilecek verinin tutulduğu bellek adresi
	- len: Gönderilecek veri uzunluğu (byte cinsinden)
	- flags: Gönderim bayrakları (çoğunlukla 0 kullanılır)
- Return değeri:
	- Pozitif sayı: Gönderilen byte sayısı.
	- 0: Bağlantı kapalı olabilir (nadir).
	- -1: Hata oldu, errno ayarlanır.
- Örnek:
	```c
	#include <stdio.h>
	#include <string.h>
	#include <sys/socket.h>

	int main()
	{
		int sockfd = /* connect edilmiş TCP soketi */;
		char msg[] = "Merhaba dünya!";

		if (send(sockfd, msg, strlen(msg), 0) < 0)
			perror("send hatası");
		else
			printf("Mesaj gönderildi.\n");

		return 0;
	}
	```

<br></br>

### `recv`

- Tanım: bir soketten veri okumak (almak) için kullanılır. TCP veya UDP gibi bağlantı tabanlı veya bağlantısız soketlerde veri almak için temel fonksiyondur.
- Syntax:
	```c
	#include <sys/types.h>
	#include <sys/socket.h>
	ssize_t recv(int sockfd, void *buf, size_t len, int flags);
	```
- Parametreler:
	- sockfd: Veri alınacak soket dosya tanımlayıcısı.
	- buf: Alınan verinin yazılacağı bellek alanının adresi.
	- len: buf için ayrılan maksimum byte sayısı.
	- flags: Alım davranışını değiştiren bayraklar (genellikle 0 kullanılır).
	- Return değeri:
		- Pozitif sayı: Okunan byte sayısı.
		- 0: Soket karşı taraf kapandı (bağlantı sonlandı).
		- -1: Hata oluştu, errno ayarlanır.
- Örnek:
	```c
	char buffer[1024];
	ssize_t bytes_received;

	bytes_received = recv(sockfd, buffer, sizeof(buffer) - 1, 0);
	if (bytes_received > 0)
	{
		buffer[bytes_received] = '\0'; // string sonlandırıcı ekle
		printf("Gelen mesaj: %s\n", buffer);
	}
	else if (bytes_received == 0)
	{
		printf("Bağlantı kapandı.\n");
	}
	else
	{
		perror("recv");
	}
	```

<br></br>

### `close`

- Tanım: close, bir dosya tanımlayıcısını (file descriptor - fd) kapatmak için kullanılır. Bu, soketler dahil tüm dosya tabanlı kaynaklar için geçerlidir.
- Syntax:
	```c
	#include <unistd.h>
	int close(int fd);
	```
- Parametre:
	- fd: Kapatılacak dosya tanımlayıcısı.
	- Bu bir:
		- Dosya
		- Soket
		- Boru (pipe)
		- Terminal olabilir.
	- Return değeri:
		- Başarılıysa: 0
		- Hatalıysa: -1 döner ve errno ayarlanır.
- Örnek:
	```c
	#include <unistd.h>
	#include <fcntl.h>
	#include <stdio.h>

	int main()
	{
		int fd = open("test.txt", O_RDONLY);
		if (fd < 0)
		{
			perror("open");
			return 1;
		}

		// dosya işlemleri...

		if (close(fd) < 0)
		{
			perror("close");
			return 1;
		}

		return 0;
	}
	```

<br></br>

### `malloc`

- Tanım: malloc (memory allocation), çalışma zamanında (runtime) heap bölgesinden istenilen miktarda bellek ayırmak için kullanılır.
- Syntax:
	```c
	#include <stdlib.h>
	void *malloc(size_t size);
	```
- Parametre:
	- size: Ayırmak istenen byte (bayt) cinsinden bellek miktarı.
	- Return değeri:
		- Başarılıysa: Ayırdığı bellek bölgesinin başlangıç adresi (void pointer).
		- Başarısızsa: NULL döner.
- Dönen değer genellikle ihtiyaca uygun türde pointer’a cast edilir.
- Örnek:
	```c
	#include <stdlib.h>
	#include <stdio.h>

	int main()
	{
		int *arr;
		arr = (int *)malloc(5 * sizeof(int)); // 5 adet int'lik bellek ayır

		if (arr == NULL)
		{
			printf("Bellek ayrılamadı!\n");
			return 1;
		}

		for (int i = 0; i < 5; i++)
			arr[i] = i * 10;

		for (int i = 0; i < 5; i++)
			printf("%d ", arr[i]);  // Çıktı: 0 10 20 30 40

		free(arr);  // Belleği serbest bırak
		return 0;
	}
	```

<br></br>

### `calloc`

- Tanım: calloc (contiguous allocation), çalışma zamanında sıfırlanmış (0 ile doldurulmuş) bellek bloğu ayırmak için kullanılır. malloc’a benzer, ama ayrılan bellek otomatik olarak 0 ile sıfırlanır.
- Syntax:
	```c
	#include <stdlib.h>
	void *calloc(size_t num, size_t size);
	```
- Parametreler:
	- num: Kaç adet öğe ayrılacak?
	- size: Her bir öğenin byte cinsinden boyutu.
	- Toplam ayrılacak bellek: num * size
	- Return değeri:
		- Başarılıysa: Bellek adresi (void *)
		- Başarısızsa: NULL
- Örnek:
	```c
	#include <stdio.h>
	#include <stdlib.h>

	int main()
	{
		int *arr;
		arr = (int *)calloc(5, sizeof(int)); // 5 adet int için sıfırlanmış bellek ayır

		if (arr == NULL)
		{
			printf("Bellek ayrılamadı!\n");
			return 1;
		}

		for (int i = 0; i < 5; i++)
			printf("%d ", arr[i]);  // Hepsi 0 yazdırır

		free(arr);  // Belleği serbest bırak
		return 0;
	}
	```

<br></br>

### `strcat`

- Tanım: strcat, bir C stringinin (null-terminated char dizisi) sonuna başka bir stringi ekler. Yani iki stringi birleştirir.
- Syntax:
	```c
	#include <string.h>
	char *strcat(char *dest, const char *src);
	```
- Parametreler:
	- dest: Üzerine yazılacak, sonuna eklenecek string. (Hedef)
	- src: dest’in sonuna eklenecek string. (Kaynak)
	- dest dizisinin sonunda yer alan \0 karakteri üzerine yazılır ve src’nin tüm karakterleri eklenir.
	- Return değeri:
		- dest pointer’ı (birleşmiş haliyle).
- Örnek:
	```c
	#include <stdio.h>
	#include <string.h>

	int main()
	{
		char str1[100] = "Merhaba ";
		char str2[] = "Dünya!";

		strcat(str1, str2);

		printf("%s\n", str1);  // Çıktı: Merhaba Dünya!

		return 0;
	}
	```

<br></br>

### `strcpy`

- Tanım: strcpy, bir C stringini (null-terminated char dizisi), başka bir diziye karakter karakter kopyalar.
- Syntax:
	```c
	#include <string.h>
	char *strcpy(char *dest, const char *src);
	```
- Parametreler:
	- dest: Kopyanın yazılacağı hedef bellek adresi.
	- src: Kopyalanacak kaynak string.
	- src’nin tüm karakterleri (sonundaki \0 dahil) dest’e kopyalanır.
	- Return değeri:
		- dest pointer’ı (kopyalanmış haliyle).
- Örnek:
	```c
	#include <stdio.h>
	#include <string.h>

	int main()
	{
		char source[] = "Merhaba";
		char dest[100];

		strcpy(dest, source);

		printf("Kopyalanan string: %s\n", dest);  // Çıktı: Merhaba
		return 0;
	}
	```

<br></br>
