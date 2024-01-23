# HSK 단어 맞추기 게임입니다. 
# 동영상 : http://bit.ly/hskprojectvideo<br>

#중국어와 한국어 단어 매핑(LanguageData.cs) 
```C#
namespace LanguageData
{
    [System.Serializable]
    public class LanguageInfo
    {
        public int index; 
        public string KoreanWord;
        public string ChineseWord;
    }

    public static class WordPair
    {
        public static List<LanguageInfo> wordPairs = new List<LanguageInfo>
        {
        new LanguageInfo {KoreanWord = "아줌마",ChineseWord = "阿姨" },
        new LanguageInfo {KoreanWord = "문장의 끝에 쓰여 감탄·찬탄 따위 등의 어기를 나타냄", ChineseWord = "啊" },
        new LanguageInfo{KoreanWord= "(키가)작다",ChineseWord ="矮"  },
        new LanguageInfo{KoreanWord="취미",ChineseWord = "爱好"  },
        new LanguageInfo{KoreanWord="조용하다",ChineseWord="安静"},
        new LanguageInfo{KoreanWord="을,를(목적어를 동사 앞에 놓을 때 쓰는 것)",ChineseWord = "把"},
        new LanguageInfo{KoreanWord="(학급의)반",ChineseWord = "班"},
        new LanguageInfo{KoreanWord="옮기다",ChineseWord = "搬"},
        new LanguageInfo{KoreanWord="방법",ChineseWord ="办法" },
        new LanguageInfo{KoreanWord="사무실",ChineseWord ="办公室"},
        new LanguageInfo{KoreanWord="반",ChineseWord ="半" },
        new LanguageInfo {KoreanWord="도와주다",ChineseWord ="帮忙"  },
            ...
        }
    }
```
#큐브 및 중국어 단어 생성 
1) 큐브 생성 
```C#
for (int i = 0; i < numberOfCubes; i++)
{

    randomIndex = Random.Range(0, availableIndexes.Count); //큐브의 위치 인덱스 갯수 중 램덤하게 그 위치갸 numberOfCubes의 갯수만큼 나옴 
    cubeCount = randomIndex;
    int posIndex = availableIndexes[randomIndex];// 큐브 위치의 갯수 중 램덤하게 위치 
                                                
    generatedRandomIndexes.Add(randomIndex);

    availableIndexes.RemoveAt(randomIndex); // 해당 인덱스 제거

     
    float spawnX = xPositions[posIndex];// 큐브의 위치 지정  //큐브 위치 갯수 중 램덤하게 위치한 것 배치 
    // randomIndex = Random.Range(0, wordList.Count);
    //List<int> posIndexes = new List<int> { posIndex };

    Vector3 spawnPosition = new Vector3(spawnX, 608.7f + 2.3f, -1107.5f);
```
랜덤하게 뽑은 수만큼 반복하여 큐브를 만들어 위치시킵니다. 

```C#
GameObject cube = Instantiate(cubePrefab, spawnPosition, rotation);
destroyMethod destroyMethodScript = cube.AddComponent<destroyMethod>();
cube.SetActive(true);
randomIndex = Random.Range(0, availableIndexes.Count);
```
큐브에 큐브가 파괴될 때 작동하도록 destroyMethod라는 스크립트를 붙여주고 큐브 위치의 갯수만큼 램덤하게 만들어지도록 합니다. 

2) 중국어 단어 부착  - 큐브 생성 후 중국어 텍스트 생성 및 머티리얼 조정 
```C#
randomIndex = Random.Range(0, wordList.Count);
LanguageInfo languageInfo = wordList[randomIndex];
string randomKoreanWord = wordList[randomIndex].KoreanWord;
GameObject wordObject = new GameObject();
TextMeshProUGUI koreanUI = GameObject.Find("wordAnswer").GetComponent<TextMeshProUGUI>();
koreanUI.font = KoreanFont;
koreanUI.text = randomKoreanWord;
```
후에 한국어 단어와 대응되는 중국어 단어 정답 및 램더하게 중국어 단어를 큐브에 붙이기 위한 작업입니다.    
```C#
GameObject textPrefab = new GameObject();
...
GameObject textObject = Instantiate(textPrefab, cube.transform);
TextMeshPro textMesh = textObject.AddComponent<TextMeshPro>();
RectTransform textRectTransform = textObject.GetComponent<RectTransform>();
textObject.layer = 7;
Vector2 newSize = new Vector2(1f, 1f); 
textRectTransform.sizeDelta = newSize;
textObject.transform.localPosition = new Vector3(textPrefab.transform.position.x, textPrefab.transform.position.y, -0.6189f);
textMesh.fontSize = 3f;
textMesh.alignment = TextAlignmentOptions.Center;
textMesh.color = Color.white;
textMesh.font = chineseFont;
textMesh.fontStyle = FontStyles.Bold;
textMesh.isOverlay = false;
textObject.SetActive(true);
textMesh.isOverlay = false;
BoxCollider boxCollider = textPrefab.AddComponent<BoxCollider>();
boxCollider.size = new Vector3(1f, 0.5f, 0.1f);
```
중국어를 큐브에 붙이고 레이어를 지정, 크기와 위치 조정, 가운데 정렬, 색깔 지정을 해줍니다.
그리고 박스 콜라이더를 첨가하고 크기를 조정해줍니다. 

3) 여분의 큐브 생성 
```C#
 if (cubes.Count != 5) // spareCUbe
 {
     List<int> validIndexes = availableIndexes
     .Where(index => index != posIndex || posIndex == 0)
     .ToList();

     int spareCubeCount = 0;
     int maxSpareCubeCount = Mathf.Min(validIndexes.Count, 2);

     for (int j = 0; j < maxSpareCubeCount; j++)
     {
         if (validIndexes.Count == 0)
             break;

         int randomIndex = Random.Range(0, validIndexes.Count);
         int randomNonSelectedIndex = validIndexes[randomIndex];
         validIndexes.RemoveAt(randomIndex);

         float nonSelectedSpawnX = xPositions[randomNonSelectedIndex];
         Vector3 nonSelectedSpawnPosition = new Vector3(nonSelectedSpawnX, 608.7f + 2.3f, -1107.5f);

         // 새로운 큐브를 생성
         spareCube = GameObject.CreatePrimitive(PrimitiveType.Cube);
         spareCube.transform.localScale = new Vector3(3.813359f, 6f, 1f);
         spareCube.transform.position = nonSelectedSpawnPosition;
         spareCube.transform.rotation = Quaternion.identity;
            ...
```
중국어 단어가 있는 큐브가 랜덤으로 위치가 선택되지 않은 곳에 여분의 큐브를 만들어줍니다. 

```C#
cubes.Add(spareCube);
cubes.Add(cube);
```
중국어 단어가 있는 큐브와 여분의 큐브를 cubes라는 리스트에 넣어줍니다. 


##큐브 전진시키기 (UpdateWord.cs, wordCreator.cs)
```C#
public void MoveForward()
{
    wordCreatorInstance = FindObjectOfType<wordCreator>();

    List<GameObject> cubes = wordCreatorInstance.GetCubesList();
    List<GameObject> spareBlocks = wordCreatorInstance.GetSpareBlocksList();
    float moveSpeed = 5f;  // 이동 속도를 조절하는 값

    foreach (GameObject cube in cubes)
    {
        if (cube == null) continue;

        float newZ = cube.transform.position.z - moveSpeed * Time.deltaTime;
        cube.transform.position = new Vector3(cube.transform.position.x, cube.transform.position.y, newZ);
    }
}
```
cubes 리스트에 저장된 큐브들이 5의 속도로 전진하게 하는 메소드를 만듭니다. 
```C#
void Update()
{
    update.MoveForward();
  
}
```
함수를 호출하여 큐브들이 앞으로 전진하게 합니다. 


#중국어 단어 중복 처리  
```C#
int randomKoreanIndex1 = wordList.FindIndex(wordList => wordList.KoreanWord == randomKoreanWord);// 인덱스 찾은 것이 한국어 인덱스와 맞으면 
 //인덱스 나옴 -1나옴 
if (i != posIndex)
{
    remainingIndices.Add(i);
}
```


구조체에서 중국어 인덱스와 한국어와 인덱스가 같은 것을 찾고 리스트에 넣어줍니다.  

```C#
if (i == 0) //첫번째 것이 맞으면
{
    if (randomKoreanIndex1 != -1) // 디자인 패턴 오브젝트 설계 
                                  //컴포넌트 아키텍쳐 
                                  //프로젝트 구조 // 만들어서 어떤 구조 설계가 되어야 함. 
    {
        // 한국어와 매칭되는 중국어 중에서 중복 피함 

        string randomChineseWord = wordList[randomKoreanIndex1].ChineseWord;

        // Debug.Log("선택한 한국어 : " + randomChineseWord);

        textMesh.text = randomChineseWord;
        //Debug.Log("선택:" + textMesh.text);
        // 이미 선택한 단어 쌍을 제외합니다.

        wordList.RemoveAt(randomKoreanIndex1);// 중복 제거 


    }

}

else // 나머지 큐브들에 대해서 랜덤하게 중국어 단어를 할당합니다. 
     //cube에 대해서 한국어와 중국어 단어 쌍을 설정하고, 나머지 cubes에 대해서 랜덤하게 중국어 단어를 할당하는 부분
{
    //randomIndex = Random.Range(0, wordPairs.Count);
    string randomChineseWord = wordList[randomIndex].ChineseWord;
    //Debug.Log("나머지 큐브 : " + randomChineseWord);
    //Debug.Log("인덱스:" + randomIndex);
    textMesh.text = randomChineseWord;
    // Debug.Log("textMesh.text:" + textMesh.text);


    // 이미 선택한 단어 쌍을 제외합니다.
    wordList.RemoveAt(randomIndex);

}
textObject.transform.parent = cube.transform;
```
한국어와 대응되는 중국어 단어가 중복되는 것을 피하고 나머지 큐브들에 대해서 랜덤하게 중국어 단어를 할당합니다. 
그리고 중국어 단어를 큐브의 자식으로 놓아서 큐브에 붙게 합니다. 

# 큐브 파괴 및 목숨과 점수 조정

```C#
    RaycastHit hit;

    if (Physics.Raycast(raycastOrigin, raycastDirection, out hit, 1.1f))
    {
        TextMeshPro cubeText = hit.collider.GetComponentInChildren<TextMeshPro>();
        TextMeshPro cubeTextChild = hit.collider.GetComponent<TextMeshPro>();

        if (cubeText != null || cubeTextChild != null)
        {
            findCubes.Add(hit.collider.gameObject);
            findText.Add(cubeText);
            findText.Add(cubeTextChild);
            string chineseCube = cubeText.text;
            Debug.Log("중국어 단어: " + chineseCube);
            Destroy(hit.collider.gameObject);
        }
        else if (!hit.collider.CompareTag("spareCubes") && (cubeText == null || cubeTextChild == null))
        {
            findCubes.Add(hit.collider.gameObject);
            lifeScore.life -= 1;
            Destroy(hit.collider.gameObject);
        }

        else if (hit.collider.CompareTag("spareCubes") && (cubeText == null || cubeTextChild == null) )
        {
            findCubes.Add(hit.collider.gameObject);
            lifeScore.life -= 1;
            Destroy(hit.collider.gameObject);
        }
    }
}
```
Oculus2 컨트롤러의 Ray Interactor가 큐브나 중국어 단어에 닿으면 큐브를 제거하는 부분입니다. 



## 목숨(life)
```C#
public TextMeshProUGUI lifeUI;
public int life;
private void Update()
{
    lifeUI.text = "×"+ life;
}

```

##목숨이 0이 되면 GameOver씬으로 가게 (wordCreator.cs) 
```C#
 void Update()
 {
     update.MoveForward();
     if (lifeScore.life == 0)
     {
         SceneManager.LoadScene("GameOver5");
     }

 }
```






## 음악 정지 및 재생 



