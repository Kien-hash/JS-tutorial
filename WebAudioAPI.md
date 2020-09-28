# Webaudio khái niệm và cách sử dụng 
Web Audio API là một tập các API liên quan đến xử lý âm thanh bên trong một _**audio context**_, được thiết kế để cho phép định tuyến theo module. Các phương pháp xử lý âm thanh cơ bản đựoc thực hiên trong các _**audio node**_, được liên kết với nhau trở thành một ***audio routing graph***. Một số nguồn - với các kiểu bố cục kênh khác nhau - được hỗ trợ ngay cả trong một ***audio context*** duy nhất. Thiết kế modul này cung cấp sự linh hoạt để tạo ra các chức năng âm thanh phứp tạp với các hiệu ứng động.

Các audio node được nối với nhau thành các chuỗi và mạng đơn giản bằng các input và output của chúng. Chúng thường bắt đầu bằng một hoặc nhiều nguồn (sources). Các nguồn cung cấp các mảng giả trị cường độ âm (các mẫu sóng âm) với một tần số lấy mẫu khá lớn, vào tầm >8000. Các nguồn có thể được tạo ra (VD như *OscillatorNode*) hoặc có thể là bản ghi từ các tệp âm thanh/video (như trong *AudioBufferSourceNode* hoặc *MediaElementAudioSourceNode*) hoặc một audio stream (*MediaStreamAudioSourceNode*).

Output của các node trên có thể được nối với đầu vào của các node khác, chúng trộn lẫn hoặc thay đổi các luồng mẫu âm thanh này thành các luồng khác nhau. VD thường thấy là ta thường nhân các mẫu với một giá trị để làm cho chúng to hơn hoặc giảm đi (*GainNode*). Sau khi âm thanh đã được xử lý cho đủ hiệu ứng dự kiến, nó có thể được liên kết với đầu vào của đích (AudioContext.destination), nơi sẽ gửi âm thanh đến loa hoặc tai nghe. Kết nối cuối cùng này chỉ cần thiết nếu người dùng được cho là nghe thấy âm thanh.

Đơn giản thì quá trình xử lý âm thanh của webaudio diễn ra như sau:
1. Tạo audio context
2. Trong audio context, tạo source - như là audio, oscillator, stream
3. Tạo node hiệu ứng, như là reverb, biquad filter, panner, compresslà
4. Chọn điểm đến cuối của âm thanh,VD loa
5. Nối node nguồn với hiệu ứng, node hiệu ứng với đích

![](https://mdn.mozillademos.org/files/16043/audio-context_.png)

Thời gian được kiểm soát với độ chính xác cao và độ trễ thấp, cho phép các nhà phát triển viết mã phản hồi chính xác với các sự kiện và có thể nhắm mục tiêu các mẫu cụ thể, ngay cả ở tốc độ mẫu cao. Vì vậy, các ứng dụng như máy đánh trống và máy trình tự đều nằm trong tầm tay.

Webaudio API cũng cho phép chúng tôi kiểm soát cách âm thanh được truyền tải theo không gian. Sử dụng một hệ thống dựa trên *source-listener model*, nó cho phép kiểm soát *panning model* và xử lý sự suy giảm do khoảng cách gây ra bởi một nguồn chuyển động (hoặc bộ nghe chuyển động).

# Web Audio API interfaces
API Web Audio có một số interface và các sự kiện liên quan, chúng tôi đã chia thành chín loại chức năng.

## 1. General audio graph definition
Vùng chứa và định nghĩa chung định hình đồ thị âm thanh trong việc sử dụng API âm thanh trên web.

### 1.1. Audio Context
>`AudioContext` đại diện cho một đồ thị xử lý âm thanh được xây dựng từ các mô-đun âm thanh được liên kết với nhau, mỗi mô-đun được biểu diễn bằng một `AudioNode`. `AudioContext` kiểm soát việc tạo các nút mà nó chứa và thực hiện xử lý hoặc giải mã âm thanh. Bạn cần tạo `AudioContext` trước khi làm bất cứ điều gì khác, vì mọi thứ diễn ra bên trong `AudioContext`.

### 1.2. AudioContextOptions 
>Từ điển `AudioContextOptions` được sử dụng để cung cấp các tùy chọn khi khởi tạo một `AudioContext` mới.

### 1.3. AudioNode
>AudioNode interface đại diện cho một mô-đun xử lý âm thanh như nguồn âm thanh (ví dụ: phần tử  ```<audio>```  hoặc ```<video>``` ), đích âm thanh, mô-đun xử lý trung gian (ví dụ:  bộ lọc như BiquadFilterNode hoặc điều khiển âm lượng như GainNode).

### 1.4. AudioParam
>`AudioParam` interface đại diện cho một tham số liên quan đến âm thanh, như một trong một AudioNode. Nó có thể được đặt thành một giá trị cụ thể hoặc một sự thay đổi trong giá trị và có thể được thiếplậpp để xảy ra vào một thời điểm cụ thể và theo một mẫu cụ thể.

### 1.5. AudioParamMap
>Cung cấp `maplike interface` cho một nhóm interface AudioParam, có nghĩa là nó cung cấp các phương thức `choEach()`, `get()`, `has()`, `key()` và `value()`, cũng như thuộc tính `size` .

### 1.6. BaseAudioContext 
>`BaseAudioContext` interface hoạt động như một định nghĩa cơ sở cho đồ thị xử lý âm thanh trực tuyến và ngoại tuyến, như được biểu diễn bởi AudioContext và OfflineAudioContext tương ứng. Bạn sẽ không sử dụng BaseAudioContext trực tiếp - bạn sẽ sử dụng các tính năng của nó thông qua một trong hai interface kế thừa này.

### 1.7. The ended event
>Sự kiện đã kết thúc được kích hoạt khi quá trình phát đã dừng lại vì đã đạt đến phần cuối của media.

## 2. Defining audio sources
Interfaces xác định nguồn âm thanh để sử dụng trong Web Audio API.

### 2.1. AudioScheduledSourceNode 
>`AudioScheduledSourceNode` là parent interface cho một số loại interface nút nguồn âm thanh. Nó là một `AudioNode`.

### 2.2. OscillatorNode 
>`OscillatorNode` interface đại diện cho một dạng sóng tuần hoàn, chẳng hạn như sóng sin hoặc sóng tam giác. Nó là một mô-đun xử lý âm thanh AudioNode tạo ra một tần số nhất định của sóng.

### 2.3. AudioBuffer 
>`AudioBuffer` interface đại diện cho nội dung âm thanh ngắn nằm trong bộ nhớ, được tạo từ tệp âm thanh bằng phương thức `AudioContext.decodeAudioData()`, hoặc được tạo bằng dữ liệu thô bằng `AudioContext.createBuffer()`. Sau khi được giải mã thành dạng này, âm thanh sau đó có thể được đưa vào `AudioBufferSourceNode`.

### 2.4. AudioBufferSourceNode 
>`AudioBufferSourceNode` interface đại diện cho một nguồn âm thanh bao gồm dữ liệu âm thanh trong bộ nhớ, được lưu trữ trong AudioBuffer. Nó là một `AudioNode` hoạt động như một nguồn âm thanh.

### 2.5. MediaElementAudioSourceNode 
>`MediaElementAudioSourceNode` interface đại diện cho nguồn âm thanh bao gồm phần tử HTML5 ```<audio>``` hoặc ```<video>```. Nó là một AudioNode hoạt động như một nguồn âm thanh.

### 2.6. MediaStreamAudioSourceNode 
>`MediaStreamAudioSourceNode` interface đại diện cho nguồn âm thanh bao gồm `MediaStream`(chẳng hạn như webcam, micrô hoặc luồng được gửi từ máy tính từ xa). Nếu có nhiều audio track trên luồng, audio track có id đứng đầu từ vựng (theo thứ tự bảng chữ cái) sẽ được sử dụng. Nó là một `AudioNode` hoạt động như một nguồn âm thanh.

### 2.7. MediaStreamTrackAudioSourceNode
> Một nút thuộc loại `MediaStreamTrackAudioSourceNode` đại diện cho một nguồn âm thanh có dữ liệu đến từ `MediaStreamTrack`. Khi tạo nút bằng phương thức `createMediaStreamTrackSource()` để tạo nút, bạn chỉ định bản nhạc sẽ sử dụng. Điều này cung cấp nhiều quyền kiểm soát hơn `MediaStreamAudioSourceNode`.

## 3. Defining audio effects filters
Các interface để xác định các hiệu ứng mà bạn muốn áp dụng cho các nguồn âm thanh của mình.

### 3.1. BiquadFilterNode
>`BiquadFilterNode` interface đại diện cho một bộ lọc bậc thấp đơn giản. Nó là một `AudioNode` có thể đại diện cho các loại bộ lọc, thiết bị điều khiển âm sắc hoặc bộ cân bằng đồ họa khác nhau. `BiquadFilterNode` luôn có chính xác một đầu vào và một đầu ra.

### 3.2. ConvolverNode 
> `ConvolverNode` interface là một AudioNode thực hiện phép tương quan tuyến tính trên một `AudioBuffer` nhất định và thường được sử dụng để đạt được `reverb effect`.

### 3.3. DelayNode
> `DelayNode` interface đại diện cho một đường trễ; một mô-đun xử lý âm thanh `AudioNode` gây ra sự chậm trễ giữa việc nhập dữ liệu đầu vào và việc truyền dữ liệu đó đến đầu ra.

### 3.4. DynamicsCompressorNode
> `DynamicsCompressorNode` interface cung cấp hiệu ứng nén, làm giảm âm lượng của các phần lớn nhất của tín hiệu để giúp ngăn chặn hiện tượng cắt và biến dạng có thể xảy ra khi phát và ghép nhiều âm thanh cùng một lúc.

### 3.5. GainNode 
> `GainNode` interface thể hiện sự thay đổi về âm lượng. Nó là một mô-đun xử lý âm thanh `AudioNode` gây ra một mức khuếch đại nhất định được áp dụng cho dữ liệu đầu vào trước khi truyền đến đầu ra.

### 3.6. WaveShaperNode 
> `WaveShaperNode` interface đại diện cho một trình biến dạng phi tuyến tính (*non-linear distorter*). Nó là một `AudioNode` sử dụng một đường cong để áp dụng sự biến dạng hình sóng cho tín hiệu. Bên cạnh các hiệu ứng biến dạng rõ ràng, nó thường được sử dụng để thêm cảm giác ấm áp cho tín hiệu.

### 3.7. PeriodicWave
>Mô tả một dạng sóng tuần hoàn có thể được sử dụng để định hình đầu ra của `OscillatorNode`.

### 3.8. IIRFilterNode
> Triển khai bộ lọc đáp ứng xung vô hạn chung (IIR); loại bộ lọc này có thể được sử dụng để triển khai các thiết bị điều khiển âm sắc và bộ cân bằng đồ họa.

## 4. Defining audio destinations
Khi bạn xử lý xong âm thanh của mình, các interfaces này xác định nơi xuất ra.

### 4.1. AudioDestinationNode 
> AudioDestinationNode interface đại diện cho điểm đến cuối cùng của nguồn âm thanh trong một `AudioContext` nhất định - thường là loa của thiết bị của bạn.

### 4.2. MediaStreamAudioDestinationNode 
> `MediaStreamAudioDestinationNode` interface đại diện cho đích âm thanh bao gồm `WebRTC MediaStream` với một `AudioMediaStreamTrack` duy nhất, có thể được sử dụng theo cách tương tự như `MediaStream` thu được từ `getUserMedia()`. Nó là một `AudioNode` hoạt động như một đích âm thanh.

## 5. Data analysis and visualization
> Nếu bạn muốn trích xuất thời gian, tần số và các dữ liệu khác từ âm thanh của mình, `AnalyserNode` là thứ bạn cần.

### 5.1. AnalyserNode
> `AnalyserNode` interface đại diện cho một node có thể cung cấp thông tin phân tích miền thời gian và tần số thời gian thực cho mục đích phân tích và trực quan hóa dữ liệu.

## 6. Splitting and merging audio channels
Để tách và hợp nhất các kênh âm thanh, bạn sẽ sử dụng các interface này

### 6.1. ChannelSplitterNode
> interface `ChannelSplitterNode` tách các kênh khác nhau của nguồn âm thanh thành một tập hợp các đầu ra đơn âm.
### 6.2. ChannelMergerNode
> interface `ChannelMergerNode` kết hợp các đầu vào mono khác nhau thành một đầu ra duy nhất. Mỗi đầu vào sẽ được sử dụng để lấp đầy một kênh của đầu ra.

## 7. Audio spatialization
Các interface này cho phép bạn thêm các hiệu ứng xoay không gian âm thanh vào nguồn âm thanh của mình.

### 7.1. AudioListener
> interface `AudioListener` thể hiện vị trí và hướng của người duy nhất đang nghe cảnh âm thanh được sử dụng trong không gian hóa âm thanh.

### 7.2. PannerNode
> interface `PannerNode` thể hiện vị trí và hành vi của tín hiệu nguồn âm thanh trong không gian 3D, cho phép bạn tạo các panning effects phức tạp.

### 7.3. StereoPannerNode
> interface `StereoPannerNode` đại diện cho một node panner âm thanh nổi đơn giản có thể được sử dụng để di chuyển luồng âm thanh sang trái hoặc phải.

## 8. Xử lý âm thanh trong JavaScript
Sử dụng bảng làm việc âm thanh, bạn có thể xác định các nút âm thanh tùy chỉnh được viết bằng JavaScript hoặc WebAssembly. Các worklet âm thanh triển khai interface Worklet, một phiên bản nhẹ của interface Worker. Các tập tin âm thanh được bật theo mặc định cho Chrome 66 trở lên.

### 8.1. AudioWorklet
> interface AudioWorklet có sẵn thông qua audioWorklet của đối tượng AudioContext và cho phép bạn thêm các mô-đun vào worklet âm thanh sẽ được thực thi ngoài luồng chính.

### 8.2. AudioWorkletNode
> interface AudioWorkletNode đại diện cho một AudioNode được nhúng vào biểu đồ âm thanh và có thể chuyển các thông báo đến AudioWorkletProcessor tương ứng.

### 8.3. AudioWorkletProcessor
> interface AudioWorkletProcessor đại diện cho mã xử lý âm thanh chạy trong AudioWorkletGlobalScope tạo, xử lý hoặc phân tích âm thanh trực tiếp và có thể chuyển các thông báo đến AudioWorkletNode tương ứng.

### 8.4. AudioWorkletGlobalScope
> interface AudioWorkletGlobalScope là một đối tượng có nguồn gốc từ WorkletGlobalScope đại diện cho ngữ cảnh công nhân trong đó chạy một tập lệnh xử lý âm thanh; nó được thiết kế để cho phép tạo, xử lý và phân tích dữ liệu âm thanh trực tiếp bằng cách sử dụng JavaScript trong một chuỗi bảng tính thay vì trên chuỗi chính.

### `Obsolete: script processor nodes`
Trước khi các tập tin âm thanh được xác định, API Web Audio đã sử dụng ScriptProcessorNode để xử lý âm thanh dựa trên JavaScript. Vì mã chạy trong luồng chính nên chúng có hiệu suất không tốt. ScriptProcessorNode được giữ lại vì lý do lịch sử nhưng được đánh dấu là không được dùng nữa và sẽ bị xóa trong phiên bản thông số kỹ thuật trong tương lai.

### ScriptProcessorNode
> interface ScriptProcessorNode cho phép tạo, xử lý hoặc phân tích âm thanh bằng JavaScript. Nó là một mô-đun xử lý âm thanh AudioNode được liên kết với hai bộ đệm, một bộ chứa đầu vào hiện tại, một bộ chứa đầu ra. Một sự kiện, triển khai interface AudioProcessingEvent, được gửi đến đối tượng mỗi khi bộ đệm đầu vào chứa dữ liệu mới và trình xử lý sự kiện kết thúc khi nó đã lấp đầy bộ đệm đầu ra với dữ liệu.
### audioprocess (event)
> Sự kiện xử lý âm thanh được kích hoạt khi bộ đệm đầu vào của Mã lệnh API xử lý âm thanh web sẵn sàng được xử lý.
### AudioProcessingEvent
> Web Audio API AudioProcessingEvent đại diện cho các sự kiện xảy ra khi bộ đệm đầu vào ScriptProcessorNode sẵn sàng được xử lý.

## 9. Offline/background audio processing
Có thể xử lý / hiển thị đồ thị âm thanh rất nhanh trong nền - hiển thị nó tới `AudioBuffer` thay vì loa của thiết bị - với những điều sau.

### 9.1. OfflineAudioContext
> interface `OfflineAudioContext` là interface `AudioContext` đại diện cho một đồ thị xử lý âm thanh được xây dựng từ các `AudioNodes` được liên kết với nhau. Ngược lại với `AudioContext` tiêu chuẩn, `OfflineAudioContext` không thực sự hiển thị âm thanh mà tạo ra nó, *nhanh nhất có thể*, trong bộ đệm.
### 9.2. complete (sự kiện)
> Sự kiện hoàn chỉnh được kích hoạt khi kết thúc việc hiển thị `OfflineAudioContext`.
### 9.3. OfflineAudioCompletionEvent
> `OfflineAudioCompletionEvent` đại diện cho các sự kiện xảy ra khi quá trình xử lý `OfflineAudioContext` bị chấm dứt. Sự kiện hoàn chỉnh thực hiện interface này.

