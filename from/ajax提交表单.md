# ajax提交表单

---


```javascript
<script>

$("#sub").click(function (e) {
    e.preventDefault();
    var box = document.querySelector("#msg");    //将data内容中htmlModel的值添加到当前的box中
    var inner = '';
    $.ajaxSetup({
        data: {csrfmiddlewaretoken: '{{ csrf_token }}'}
    });
    $.ajax({
        type: 'post',
        url: "{% url 'froms:po' %}",
        data: {
            'phoneNum': $('#id_phoneNum').val(),  //获取content文本框中的值
            'name': $('#id_name').val(),
            'email': $('#id_email').val(),
        },
        dataType: 'json',
        success: function (data) {
            // 评论提交成功
            // 判断是否有错误信息
            console.log(data);
            for (var i in data) {
                console.log(i);
                console.log(data[i]);
                inner += data[i]
            };
            box.innerHTML = inner;
        },
        error: function (data) {
            alert("Something is wrong! lol");
        }
    });
});

</script>

```


views.py
```python
def po(request):
    fo = User(request.POST)
    if fo.is_valid():
        fo.save()
        return JsonResponse({'data':'ok'})
    else:
        return JsonResponse(fo.errors)

```