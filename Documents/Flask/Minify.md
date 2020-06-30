## Minify
프론트 화면을 구성하는 요소는 다양하다. 그 중 대표적인 것으로 `css`, `js` 등이 있는데, 이들은 정적 컨텐츠라 불리우며, `Static` 폴더에 주로 저장하게 된다. 이런 정적 컨텐츠들은 대체로 프론트에서 구성하는 컨텐츠들이 다양해지거나, 필요한 코드가 많아질수록 파일의 크기인 코드의 크기가 커지게 되는데 그 크기가 커지는 만큼 클라이언트에게 전달되는 시간이 증가하게 된다.  
클라이언트에게 전달되는 시간이 증가하는 이유는, 다른 컨텐츠도 동일하겠지만, 동적 컨텐츠는 컨텐츠를 전달 받아 동적으로 생성되는 시간과 달리, 네트워크로 오로지 그 크기만큼 전달되는 정적 컨텐츠는 크기가 커질수록 네트워크로 전달되는 시간이 증가하게 된다.  
이런 문제를 해소하기 위해서는 코드 경량화(Minify)를 사용하는데, 주로 `CSS`, `JS` 파일을 경량화한다. 아래는 생활코딩에서 나온 코드 경량화(Minify)의 예시이다.

### Minify 전 (원본)
```css
body{

}
h1{
    color:tomato;
}
```
### Minify 후
```css
h1{color:tomato}
```

단순히 보면 단지 코드의 공백을 제거하고 내용이 없는 `body`를 제거 한 것이 아니냐라고 볼 수 있는데, 그렇다고 보아도 된다. 또, 그럼 `minify`를 하지 않고 `minify` 결과처럼 한줄로 `css`를 작성하고 사용하지 않은건 제거하면서 작성하면 되지 않나라고 생각하면 쉽지만, 실제로 업무를 진행하다보면 한줄로 모든 코드를 작성하는건 상당히 어렵다. 특히 위 예시는 `css`지만, `js`를 한 줄로 작성한다고 생각해보면.. 나는 절대로 할 수 없다.  
이와 같은 것을 `Minify` 코드 경량화라고 하는데, 코드 경량화를 하는 방법은 굉장히 많다. 그 중 `python`을 사용해서 `Minify`하는 코드를 아래에 작성했다. 사용된 라이브러리는 `rjsmin`과 `rcssmin`이다. (라이브러리명을 보면 알 수 있듯이 `rjsmin`은 `js`를 경량화하며, `rcssmin`은 `css`를 경량화한다.)



```python
import os
import rjsmin
import rcssmin

def name_minify(name, file_type):
    return name.replace(file_type, 'min.%s' % file_type)


def minify(file_type='css'):
    path = os.path.join('static', file_type)
    for source in os.listdir(path):
        if source.endswith(file_type) and not source.endswith('min.%s' % file_type):
            source_path = os.path.join(path, source)
            dest_path = os.path.join(path, name_minify(source, file_type))
            with open(source_path, 'r') as infile, open(dest_path, 'w') as outfile:
                if file_type == 'css':
                    outfile.write(rcssmin.cssmin(infile.read()))
                elif file_type == 'js':
                    outfile.write(rjsmin.jsmin(infile.read()))
                else:
                    continue
                print("%s minified to %s" % (source_path, dest_path))
```
