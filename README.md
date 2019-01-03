# praat Centos7 설치 ( https://github.com/jinserk/pytorch-asr/wiki/How-to-install-Praat-on-CentOS7 )

$sudo yum install gtk2-devel pulseaudio-libs-devel alsa-lib-devel jack-audio-connection-kit-devel
$sudo yum --enablerepo=extras install centos-release-scl
$ sudo yum install devtoolset-7
$ scl enable devtoolset-7 bash
$ git clone https://github.com/praat/praat.git
$ cd praat
$ cp makefiles/makefile.defs.linux.pulse ./makefile.defs
$ make
$ make install

praat 을 실행시켜보면 오류가 뜰텐데 잘못설치되어서 나는 오류가 아니다.
Windows용 prrat을 설치후에, 파일을 읽어서 그래프로 출력해 보고, 텍스트 파일로 변환도 해본다.
그리고나서, 방금했던 작업을 스크립트로 짜본다. 윈도우  GUI에서 실행이 잘 되는것을 확인 후에 이 스크립트를 리눅스로 옮겨 온다.

==a.script==
Read from file: "836271.wav"
Save as text file: "836271.TextGrid"

실행하는 폴더에 836271.wav파일과 a.script가 있다고 가정했을때 실행방법은
$ praat a.script
실행하면 오류가 뜨지 않고 정상종료 되면서 836271.TextGrid라는 파일이 생성된다. ( 저는 잘 됬습니다.~_~;;)

즉 실행방법은 스크립트를 먼저 짜고 praat로 스크립트를 실행하면 된다고 이해할 수 있었다.
스크립트는 아주 직관적이라서 배우기 쉽다. 검색해서 나온부분에서 이해가 안되는 부분이 없을정도.

먼저 콘솔실행시 인자값 (Arguments)를 받는 방법
form Test command line calls
        sentence filename
        real Beep_duration
        sentence Second_text
endform
sentence는 string이고 real은 실수를 말하는듯 하다.(안찾아봤습니다. 일단 실행하는게 목적입니다.)

즉 저렇게 스크립트를 짜면 prrat a.script 1 2 3을 했을때 filename=1 real=2 Second_text=3으로 들어가는것이다.
변수를 쓸때는 filename$ 처럼 사용하는듯 하다.
주석은 #



스크립트완성본. wav파일에서 pitch(주파수 높낮이Hz) intensity(목소리 크기)를 출력해준다.

form Test command line calls
        sentence filename
        real Beep_duration
        sentence Second_text
endform
Read from file: filename$
Rename: "Sound"
sound = selected ("Sound")
tmin = Get start time
tmax = Get end time
To Pitch (shs): 0.001, 50.0, 15, 1250.0, 15, 0.84, 600.0, 48
Rename: "pitch"
selectObject: sound
To Intensity: 75, 0.001
Rename: "intensity"
#writeInfoLine: "Here are the results:"
for i to (tmax-tmin)/0.05
    time = tmin + i * 0.05
    selectObject: "Pitch pitch"
    pitch = Get value at time: time, "Hertz", "Linear"
    selectObject: "Intensity intensity"
    intensity = Get value at time: time, "Cubic"
    appendInfoLine: fixed$ (time, 2), " ", fixed$ (pitch, 3), " ", fixed$ (intensity, 3)
endfor

웹서버에서 실행하려고 하면 오류가 떴는데
/home/내아이디 폴더에 .praat-dir 이라는 디렉토리를 만들수있는 권한이 없다고 해서 ( Cannot create directory /home/내아이디/.praat-dir )
그냥 ~ 폴더에 chmod  777 줘버렸다.
그러고 나서 잘 실행된다.


