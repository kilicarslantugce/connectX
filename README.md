ConnectX Nedir ?

ConnectX connect-four oyununu taban olarak alır. Connect-Four, oyuncuların sırayla 7 sütun boyunca ve 6 sıra yüksekliğinde dikey bir tahtaya parçaları yerleştirdiği tic-tac-toe benzeri iki oyunculu bir oyundur. Her iki oyuncu da 21 aynı taşla başlar ve parçalar, sütun içinde mevcut en düşük alanı kaplayarak, doğrudan aşağıya düşer. Oyunun amacı, kişinin kendi disklerinden dördünün yatay, dikey veya çapraz bir çizgisini oluşturan ilk kişi olmaktır.

Kaggle’ın ConnectX projesi ile kullanıcılardan istediği; Connect Four'u oynayabilecek ve başka bir oyuncuya karşı kazanabilecek bir ajan yaratmalarıdır.

Bu projede Reinforcement learning yaklaşımı kullanılır.

Reinforcement Learning
Pekiştirmeli öğrenme, ön bilgi yerine gözlem ve seçimde bulunmak üzerine kuruludur. Neden sonuç ilişkisinin nasıl kurulduğu bilgisi yapay zekâya verilmez ya da çok az verilir, yapay zekâ bu ilişkiyi kendisi öğrenir. 

Projede Kullanılan Teknolojiler

Kaggle-Environments
Bölümleri değerlendirmek için Kaggle Ortamları oluşturuldu. Diğer kütüphaneler arayüz  belirlerken , bu kütüphane şunlara odaklanır: 
    • Bölüm değerlendirmesi (training ajanlarını kıyaslar).
    • Yapılandırılabilir ortam / ajan yaşam döngüleri.
    • Basitleştirilmiş ajan ve ortam oluşturma. 
    • Çapraz dil uyumlu / kopyalanabilir sözdizimi / arayüzler. 

Environments: Oyunun oynandığı ortam.
Kaggle-Environments ile import edilen fonksiyonlar:
    • make(),
    • evaluate(),

Oyun ortamı oluşturmak için Kaggle-Environments kütüphanesinden, make(environment, configuration={}, steps=[], debug=False) fonksiyonu çağırırlır.
Args:
	environment (str|Environment),
	configuration (dict, optional),
	steps (list, optional),
	debug (bool=False, optional)

Returns: Environment: Belirli bir ortamın örneği.


ConnectX için oluşturulmuş ortam make("connectx", debug=True) ile çağırılır

Bir veya daha fazla bölümü  değerlendirmek için evaluate(environment, agents=[], configuration={}, steps=[], num_episodes=1) fonksiyonu çağırılır. Bu fonksiyonda ortam ve ajan kombinasyonu vardır.
Args:
        environment (str|Environment, 
        agents (list),
        configuration (dict, optional),
        steps (list, optional),
        num_episodes (int=1, optional): kaç bölümün yürütüleceği belirtilir.

Returns: list of int: Tüm bölümler ve tüm ajanlar için nihai ödüllerin listesi.
Agents(Ajanlar)
Bir gözlem verilen bir işlev bir eylem oluşturur. 
Ajan işlevlerinin observation(gözlem) ve configuration(yapılandırma) parametreleri olabilir. Geçerli bir eylem döndürmeleri gerekir. observation, configuration ve eylemlerle ilgili ayrıntılar, spesifikasyonu görüntüleyerek görülebilir. 
ConnectX projesinde yaratılan ajanı test etmek için ikinci oyuncu olarak tanımlanmış default iki ajan vardır. Bunları görüntülemek içn print(*env.agents) kodu çalıştırılır. Default ajanlar random ve negamax dir.

Ajan Yaratmak
Submission oluşturmak için, bir ajan işlevi tamamen kapsüllenmelidir (harici bağımlılıklar olmamalıdır).
Yaratılan ajan observation ve configuration olarak iki parametre alır.
Observation: env.specification.observation ile içeriği görüntülenir. Ortam ve ajanın bilgilerini içerir.
{'board':{'tanımlama': 'Serileştirilmiş kılavuzdur (satır x sütun).'
0 = Empty, 1 = P1, 2 = P2 demektir.
'type': 'array', 'default': []},
'mark': {'default': 0, 'tanımlama': 'Hangi damanın ajan olduğunu belirtir.', 'enum': [1, 2]}}


Configuration: env.specification.configuration  ile içeriği görüntülenir. Oyunun kaba kurallarını tutar.
{'timeout': {'tanımlama': 'Bir ajanın zaman aşımına uğramadan önce çalışabileceği saniye sayısı.', 'type': 'integer', 'minimum': 1, 'default': 5},
 'columns': {'tanımlama': 'Tahtadaki sütun sayısı.', 'type': 'integer', 'default': 7, 'minimum': 1},
 'rows': {'tanımlama': 'Tahtadaki satır sayısı.', 'type': 'integer', 'default': 6, 'minimum': 1},
 'inarow': {'tanımlama': 'Kazanmak için arka arkaya gelmesi gereken taş sayısı.', 'type': 'integer', 'default': 4, 'minimum': 1},
 'steps': {'tanımlama': 'Ortamın çalıştırabileceği maksimum adım sayısı.', 'type': 'integer', 'minimum': 1, 'default': 1000}}

Ajan tanımlamadan önce, belirli bir hareketin oyunu nasıl etkileyebileceğini belirleyen bazı fonksiyonlar tanımlanır. Bunlar yardımcı fonksiyonlardır.


Yardımcı fonksiyonlar ve bunlara ilave fonksiyonlar
    • drop_piece(grid, col, piece, config): Ajan parçayı seçili sütuna düşürürse sonraki adımda ne yapacağını belirler.
            ▪ grid: 6x7 boyutlarındaki oyun tahtasının her bir hücresi,
            ▪ col : Sütun bilgisi.
            ▪ piece: Bir sonraki adımda hangi satır ve sütunun seçileceğini belirtir.
            ▪ config: Oyun ile ilgili kuralları tutar.
    • count_windows(grid, num_discs, piece, config): get_heuristic() fonksiyonuna yardımcı fonksiyondur. Belirlenen sezgisel koşulları karşılayan pencere sayısını sayar.
            ▪ grid: 6x7 boyutlarındaki oyun tahtasının her bir hücresi,
            ▪ num_discs : Disk sayısını tutar.
            ▪ piece: Bir sonraki adımda hangi satır ve sütunun seçileceğini belirtir.
            ▪ config: Oyun ile ilgili kuralları tutar.
    • is_terminal_window(window, config): Ajanın veya rakibin pencerede arka arkaya dört tane olup olmadığını kontrol eder .
            ▪ window: O an oyunun oynandığı pencere.
            ▪ config: Oyun ile ilgili kuralları tutar.
    • is_terminal_node(grid, config): Minimax için yardımcı fonksiyondur. Oyunun bitip bitmediğini kontrol eder.
            ▪ grid: 6x7 boyutlarındaki oyun tahtasının her bir hücresi.
            ▪ config: Oyun ile ilgili kuralları tutar.
    • check_window(window, num_discs, piece, config): Get_heuristic için yardımcı fonksiyon: pencerenin sezgisel koşulları karşılayıp karşılamadığını kontrol eder 
            ▪ window: O an oyunun oynandığı pencere.
            ▪ num_discs :  Disk sayısını tutar.
            ▪ piece: Bir sonraki adımda hangi satır ve sütunun seçileceğini belirtir.
            ▪ config: Oyun ile ilgili kuralları tutar.
    • get_heuristic(grid, mark, config):
            ▪ grid: 6x7 boyutlarındaki oyun tahtasının her bir hücresi,
            ▪ mark:
            ▪ config: Oyun ile ilgili kuralları tutar.

Bu yardımcı ve ilave fonksiyonlar kullanılarak ajan tanımlaması yapılır.

Ajanı oluşturan fonksiyonlar:
    •  minimax(node, depth, maximizingPlayer, mark, config): Seçili sütundaki düşen parçanın değerini hesaplamak için minimax kullanır.
            ▪ node: 6x7 boyutlarındaki oyun tahtasının her bir hücresi,
            ▪ depth : Hamleler bir ağaçta tutulur. Ağacın derinliğini tutar.
            ▪ MaximizingPlayer: En iyi oyuncu,
            ▪ mark:
            ▪ config: Oyun ile ilgili kuralları tutar.

    • score_move(grid, col, mark, config, nsteps): 
            ▪ grid :6x7 boyutlarındaki oyun tahtasının her bir hücresi.
            ▪  Col: Sütun bilgisi,
            ▪ mark:
            ▪ config: Oyun ile ilgili kuralları tutar.
            ▪ Nsteps: Kaçıncı adımda olduğunu tutar.

    • agent_minimax(obs, config):
            ▪ obs: Ortam ve ajanın bilgilerini içerir.
            ▪ config: Oyun ile ilgili kuralları tutar.
 
Training(Eğitim)
Open AI Gym arayüzü, eğitim ajanlarına yardımcı olmak için kullanılır. None anahtar sözcüğü, aşağıda hangi aracının eğitileceğini belirtmek için kullanılır. (yani, connectx'in birinci veya ikinci oyuncusu olarak eğitin). 



 



 

## REFERENCES

    1.  https://www.kaggle.com/yegorbiryukov/cell-swarm/data
    2.  https://www.kaggle.com/lbarbosa/connectx-deep-reinforcement-learning
    3.  https://www.kaggle.com/koutetsu/connectx-multi-agent-reinforcement-learning-2
    4. https://www.kaggle.com/koutetsu/connectx-multi-agent-reinforcement-learning-1
    5. .https://en.wikipedia.org/wiki/Reinforcement_learning
    6. https://www.kaggle.com/kieranhobden/reinforcement-learning-in-connectx
    7. https://github.com/Kaggle/kaggle-environments#:~:text=Kaggle%20Environments%20was%20created%20to,(compared%20to%20training%20agents)
    8. https://www.kaggle.com/olivermoody/connectx-reinforcement-learning
    9. https://matplotlib.org/
