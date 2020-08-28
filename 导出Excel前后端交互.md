```
@interaction_blueprint.route('import/task_lesson/excel', methods=['GET'])
def import_task_lesson_excel():
    rep = reqparse.RequestParser()
    rep.add_argument('start_date', required=True)
    rep.add_argument('end_date', required=True)

    rep.add_argument('level', type=int, required=True)

    rep.add_argument('unit_to', type=int, required=True)
    rep.add_argument('unit_from', type=int, required=True)

    rep.add_argument('lesson_to', type=int, required=True)
    rep.add_argument('lesson_from', type=int, required=True)

    rep.add_argument('task_id', required=True)
    args = rep.parse_args()

    start_time, end_time = str_to_timestamp(args.start_date, args.end_date)

    if end_time < start_time:
        raise_400_response(message='结束时间必须大于开始时间')

    lesson_from = args.lesson_from
    lesson_to = args.lesson_to
    unit_from = args.unit_from
    unit_to = args.unit_to

    if int(lesson_from) < 10 and int(lesson_from) % 2 != 0:
        lesson_from += 20

    if int(lesson_to) < 10 and int(lesson_to) % 2 != 0:
        lesson_to += 20
    if lesson_to >= lesson_from:
        lesson_info = tuple(range(lesson_from, lesson_to + 1))
    else:
        lesson_info = tuple(range(lesson_to, lesson_from + 1))

    # mysql连接
    connection = PyMysql(current_app.config['MYSQL_IP'], current_app.config['MYSQL_USER'],
                         current_app.config['MYSQL_PASSWORD'],
                         current_app.config['MYSQL_DB_NAME'], current_app.config['MYSQL_PORT'])

    result = []
    start_date = datetime.strptime(args.start_date, '%Y-%m-%d')
    end_date = datetime.strptime(args.end_date, '%Y-%m-%d')
    # 由于数据表是根据月份分表的所以按月查找数据
    for item in iterator_monthly(start_date.date(), end_date.date()):
        year_month = str(item.year) + '_' + str(item.month)
        if int(item.month) <= current_app.config['MYSQL_MODEL_NAME_MONTH_SUFFIX'] and \
                int(item.year) <= current_app.config['MYSQL_MODEL_NAME_YEAR_SUFFIX']:
            task_list = connection.query_lesson_interaction_rate_by_task(
                start_time, end_time, args.level, unit_from, lesson_info, unit_to, args.task_id)
        else:
            task_list = connection.query_lesson_interaction_rate_by_task_model_suffix(
                year_month, start_time, end_time, args.level, unit_from, lesson_info, unit_to, args.task_id)
        result += task_list

    titles = [
        'task_id',
        'task_name',
        'lesson_info',
        'game_id',
        'avg_conver_percent'
    ]

    excel = Writer()
    sheet_name = '模板夸课交互率'
    excel_row = 0
    excel.write_row(sheet_name, excel_row, titles)
    excel_row += 1
    for r in result:
        interaction_rate_info = [
            r['task_id'],
            r['name'],
            r['lesson_info'],
            r['game_id'],
            r['conver_percent']
        ]
        excel.write_row(sheet_name, excel_row, interaction_rate_info)
        excel_row += 1

    res = excel.get_bytes()
    response = make_response(res)
    response.headers['Content-Type'] = 'application/ms-excel'
    response.headers["Content-Disposition"] = 'attachment;filename={0}.xlsx'.format('filename')
    return response

```

```
<button type="submit" class="btn btn-primary" onclick="import_excel()" >模板夸课交互率导出excel</button>
function import_excel() {
            alert('excel正在导出中。请勿重复点击');
            var task_id = sessionStorage.getItem('task_id');
            var start_date = sessionStorage.getItem('start_date');
            var end_date = sessionStorage.getItem('end_date');
            var level = sessionStorage.getItem('level');
            var lesson_from = sessionStorage.getItem('lesson_from');
            var lesson_to = sessionStorage.getItem('lesson_to');
            var unit_from = sessionStorage.getItem('unit_from');
            var unit_to = sessionStorage.getItem('unit_to');
            var url = '/import/task_lesson/excel?task_id=' + task_id + '&start_date=' + start_date + '&end_date='
                + end_date + '&level=' + level + '&lesson_from=' + lesson_from + '&lesson_to=' + lesson_to + '&unit_from='
                + unit_from + '&unit_to=' + unit_to;
            var xhr = new XMLHttpRequest();
            xhr.open('GET', url, true);        // 也可以使用POST方式，根据接口
            xhr.responseType = "blob";    // 返回类型blob
            // 定义请求完成的处理函数，请求前也可以增加加载框/禁用下载按钮逻辑
            xhr.onload = function () {
                // 请求完成
                if (this.status === 200) {
                    // 返回200
                    var blob = this.response;
                    var reader = new FileReader();
                    reader.readAsDataURL(blob);    // 转换为base64，可以直接放入a表情href
                    reader.onload = function (e) {
                        // 转换完成，创建一个a标签用于下载
                        var a = document.createElement('a');
                        a.download = 'task_lesson_interaction_rate.xlsx';
                        a.href = e.target.result;
                        $("body").append(a);    // 修复firefox中无法触发click
                        a.click();
                        $(a).remove();
                    }
                }
            };
            // 发送ajax请求
            xhr.send()
        }

```
