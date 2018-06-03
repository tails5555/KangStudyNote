# Proxy Pattern Issues

![proxy_example](/Application_Computer_Science/8_Object_Oriented_Pattern/img/proxy_example.png)

> Proxy Pattern을 잘 못 오용하면 이렇게 될 수 있다.

이 장면은 짱구 1기 아빠 회사에 가자 에피소드에서 따온 일부 내용이다. 여기서 짱구가 아침 부부싸움 전에서 봉미선 씨와 신형만 씨가 중간에 가정 살림과 회사 업무로 인한 충돌로 인해 서로 불만 사항을 이야기 하고 있는데 중간에 짱구가 각자 단점을 끼어 들어 둘의 사이를 더욱 흐트러 놓았다.

이 만화 내용이 단순한 만화 내용이라 유치할 수 있지만, Proxy Pattern에서 쓰이는 Proxy(대리자)의 중요성을 알 수 있는 사례로 손꼽을 수 있다. 

이번 스터디 노트에서는 Proxy Pattern에 대해 어떤 구조로 되어 있는지 공부하는 시간을 가져보도록 하자.

## What Is Proxy Pattern?

![proxy_pattern_uml](/Application_Computer_Science/8_Object_Oriented_Pattern/img/proxy_pattern_uml.png)

Proxy Pattern은 소프트웨어 디자인 패턴 중에서 **구조 패턴(Structure Pattern)** 을 구성하는 방법 중 하나이다. Proxy의 영문 뜻은 대리의 의미를 가져서 네트워크 기술에서 프로토콜에 있어서 대리 응답 등에서 친숙한 개념이다.

방금 짱구가 봉미선 씨와 신형만 씨가 싸우고 있을 때 서로의 단점을 언급하면서 싸움을 부추기는 방향으로 진행하듯이 Proxy는 어느 프로세스 내부에서 다른 무언가와 이어지는 Interface의 역할을 하는 **인터페이스 격 클래스**로 볼 수 있다.(어지간히 Interface와는 다른 개념이다.) 

Proxy Pattern을 사용하는 사례는 다음과 같다.

> Authorization 처리(인가 처리 / 보호), AOP(Aspect Oriented Programming)의 구성 시발점, 생성 자원에 대한 Background 처리, 원격 메소드를 호출하기 위한 작업 etc.

Spring Framework를 공부했다면 Proxy Pattern에 친숙할 것이다. AOP를 기반으로 Transaction, Security, Cache 처리 등에 대하여 어노테이션으로 작성하여 구조를 더욱 보기 좋게 정리할 수 있다.

## Main Proxy Example

Spring Web MVC에서 Controller와 Service의 사이를 사례로 들어보자. 

처음으로 Service Bean을 이용해야 하는데 Service에서 꼭 필요한 함수를 정리하는 Interface를 `Subject Interface`로 추상화를 시켜 의존하게 만든다.

그 다음으로 Service 인터페이스를 상속 시켜서 추상 메소드들에 대해 구현을 하는 ServiceImpl 클래스를 작성하는 것이 Real `Subject Class`를 작성하는 과정이다.

최종적으로 Web Application이 실행될 때 RestController의 요청 URI를 이용해서 데이터를 접근할 때 이용하는 객체를 Proxy 객체로 작성하면 REST API로 데이터를 가져올 수 있다.

**Subject Interface 작성**

```
public interface MusicService{
    public List<Music> findAll();
    public Music findOne(long id);
    public void create(MusicVO musicVO);
}
```

**Real Subject Class 작성**

```
@Service
public class MusicServiceImpl implements MusicService{
    @Autowired MusicRepository musicRepository;

    @Override
    public List<Music> findAll(){
        return musicRepository.findAll();
    }

    @Override
    public Music findOne(long id){
        Optional<Music> music = musicRepository.findById(id);
        return (music.isPresent()) ? music.get() : null;
    }

    @Override
    public void create(MusicVO musicVO){
        Music createMusic = musicVO.builtToMusic();
        musicRepository.save(createMusic);
    }
}
```

**Proxy Object 작성**

```
@RestController
@CrossOrigin
@RequestMapping("music")
public class MusicController{
    MusicService musicService;

    @Autowired
    public MusicController(MusicService musicService){
        this.musicService = musicService;
    }

    @GetMapping("all_musics")
    public List<Music> findAll(){
        return musicService.findAll();
    }

    @GetMapping("one_music/{id}")
    public Music findOne(@PathVariable long id){
        return musicService.findOne(id);
    }

    @PostMapping("create_music")
    public String create(@RequestBody MusicVO musicVO){
        musicService.create(musicVO);
        return "Music Create Is Success!!!";
    }
}
```

**Main Class 정의**

Spring Web MVC 설정을 마친 Application Main 클래스에는 Domain, Repository, Service, Controller 등의 Component Bean은 이미 작성이 되어 있다. 우리가 Web Application을 가동할 때 
## References
- https://ko.wikipedia.org/wiki/%ED%94%84%EB%A1%9D%EC%8B%9C_%ED%8C%A8%ED%84%B4 - Proxy Pattern 위키 백과 참조. 한글보다 영어를 참고하면 더욱 도움된다.
- https://m.blog.naver.com/gracefulife/220650929598 - 예제가 나와있지만, 본인은 Spring Web MVC에서 적용하여 재구성하였다.