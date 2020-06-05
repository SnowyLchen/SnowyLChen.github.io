---
title: java数据与Excel表格的互转
date: 2019-11-03 16:53:12
tags:
- java
- Excel
categories: java
---

### 一、导入必要的包

- <a href="/upload/Excel/poi-3.17.jar" target="_blank">poi-3.17.jar</a>
- <a href="/upload/Excel/poi-ooxml-3.17.jar" target="_blank">poi-ooxml-3.17.jar</a>
- <a href="/upload/Excel/poi-ooxml-schemas-3.17.jar" target="_blank">poi-ooxml-schemas-3.17.jar</a>
- <a href="/upload/Excel/poi-scratchpad-3.17.jar" target="_blank">poi-scratchpad-3.17.jar</a>

### 二、生成Excel

- #### 编写方法

```java
public class CreateExcel {
    private People people;
    private double govpay;
    private double peopay;
    private double cost;
    private String path;
    private String condition;
    private String rcondition;
    
	····省略所有的Getter和Setter方法

    public static boolean createSheet(OutputStream os,List<CreateExcel> list) throws IOException {
        // 创建工作薄
        HSSFWorkbook wb = new HSSFWorkbook();
        // 在工作薄上建一张工作表
        HSSFSheet sheet = wb.createSheet();
		// HSSFFont font=wb.createFont();
        HSSFCellStyle style = wb.createCellStyle();
        //把字体应用到当前的样式
        HSSFRow row = sheet.createRow((short) 0);
        row.setRowStyle(style);
        sheet.createFreezePane(0, 1);
        cteateCell(wb,style, row, (short) 0, "姓名");
        cteateCell(wb,style, row, (short) 1, "职业");
        cteateCell(wb,style, row, (short) 2, "医院");
        cteateCell(wb,style, row, (short) 3, "是否首次");
        cteateCell(wb,style,row, (short) 4, "共消费");
        cteateCell(wb,style,row, (short) 5, "预计结果");
        cteateCell(wb,style, row, (short) 6, "政府报销");
        cteateCell(wb,style, row, (short) 7, "应支付");
        cteateCell(wb,style, row, (short) 8, "具体条件取值");
        cteateCell(wb,style, row, (short) 9, "覆盖条件");
        cteateCell(wb,style, row, (short) 10, "基本路径");

        sheet.setColumnWidth(0, "姓名".getBytes().length*2*256);
        sheet.setColumnWidth(1, "职业".getBytes().length*2*256);
        sheet.setColumnWidth(2, "医院".getBytes().length*2*256);
        sheet.setColumnWidth(4, "共消费".getBytes().length*2*256);
        sheet.setColumnWidth(5, "预计结果".getBytes().length*2*256);
        sheet.setColumnWidth(6, "政府报销".getBytes().length*2*256);
        sheet.setColumnWidth(7, "应支付".getBytes().length*2*256);
        sheet.setColumnWidth(8, "具体条件取值".getBytes().length*3*256);
        sheet.setColumnWidth(9, "覆盖条件".getBytes().length*3*256);
        sheet.setColumnWidth(10, "基本路径".getBytes().length*3*256);
        for (int i = 0; i < list.size();i++) {
            HSSFRow rowi = sheet.createRow((short) (i+1));
            cteateCell(wb,style,rowi, (short) 0, list.get(i).getPeople().getName());
            cteateCell(wb,style, rowi, (short) 1, checkPojo(list.get(i).getPeople().getEmp()));
            cteateCell(wb,style, rowi, (short) 2, checkPojo(list.get(i).getPeople().getHos()));
            cteateCell(wb,style, rowi, (short) 3, String.valueOf(list.get(i).getPeople().isNum()));
            cteateCell(wb,style, rowi, (short) 4, String.valueOf(list.get(i).getCost())+"元");
            cteateCell(wb,style, rowi, (short) 5, String.valueOf(list.get(i).getPeopay())+"元");
            cteateCell(wb,style, rowi, (short) 6, String.valueOf(list.get(i).getGovpay())+"元");
            cteateCell(wb,style, rowi, (short) 7,String.valueOf(list.get(i).getPeopay())+"元");
            cteateCell(wb,style, rowi, (short) 8,String.valueOf(list.get(i).getCondition()));
            cteateCell(wb,style, rowi, (short) 9,String.valueOf(list.get(i).getRcondition()));
            cteateCell(wb,style, rowi, (short) 10,String.valueOf(list.get(i).getPath()));
        }
        wb.write(os);
        os.flush();
        os.close();
        System.out.println("文件生成成功！");
        return true;
    }

    private static void cteateCell(HSSFWorkbook wb, HSSFCellStyle cellStyle,HSSFRow row, short col, String val) {
        @SuppressWarnings("deprecation")
        HSSFCell cell = row.createCell(col);
        cell.setCellValue(val);
        cellStyle = wb.createCellStyle();
        cellStyle.setAlignment(HorizontalAlignment.CENTER_SELECTION);
        cell.setCellStyle(cellStyle);
    }

    public static  void creatStream(List<CreateExcel> list) throws IOException {
        // Excel文件名
        String name = System.currentTimeMillis()+ ".xls";
        FileOutputStream outputStream = new FileOutputStream(name);
        CreateExcel.createSheet(outputStream, list);
    }
}
```

- #### 调用函数

```java
List<CreateExcel> list=new ArrayList<>();
CreateExcel ce=new CreateExcel();
ce.set···（···）;
list.add(ce);
CreateExcel.creatStream(list);
```



### 三、读取Excel

```java

public class ReadExcelUtils {

    private Workbook workbook;

    private Sheet sheet;

    private Row row;

    private Cell cell;
    public ReadExcelUtils(){}

    public ReadExcelUtils(String excelpath) {
        if (excelpath==null){
            return;
        }
        try {
            String format = excelpath.substring(excelpath.lastIndexOf("."));
            if (format.equals(".xls")||format.equals(".xlsx")){
                InputStream is=new FileInputStream(excelpath);
                workbook= new XSSFWorkbook(is);
            }else {
                return;
            }
        }catch (Exception e){
            e.printStackTrace();
        }

    }

    public Map<Integer,Map<Integer,Object>> readExcelContext() throws Exception {
        if (workbook==null){
            throw  new Exception("wookbook为null");
        }
        //创建一个map集合用来存放excel的内容
        Map<Integer,Map<Integer,Object>> context=new HashMap<>();
        //第一张表
        sheet=workbook.getSheetAt(0);
        //获取总行数
        int RowNum=sheet.getLastRowNum()+1;
        System.out.println("rowNUm="+RowNum);
        //获取第一行
        row=sheet.getRow(0);
        //获取实际列数
        int CellNum=row.getPhysicalNumberOfCells();
        for (int i=1;i<RowNum;i++){
            row=sheet.getRow(i);
            int j=0;
            Map<Integer,Object> cellVal=new HashMap<>();
            while (j<CellNum){
                 cell=row.getCell(j);
                cellVal.put(j,cell);
                j++;
            }
            context.put(i,cellVal);
        }

        return context;
    }

}
```

- #### main函数里这么写

```java
 	@Test
    public void testBunchIns() throws Exception {

        String excelPath="H:/Java framework/Examination/src/com/cj/Utils/data/test4010.xlsx";
        ReadExcelUtils re=new ReadExcelUtils(excelPath);
        Map<Integer,Map<Integer,Object>> excelVal=re.readExcelContext();
        List<User> users=new ArrayList<>();

        for(Integer row:excelVal.keySet()){//row从1开始
            //System.out.println("excel:"+excelVal.get(row));
            User user=new User();
            user.setName(String.valueOf(excelVal.get(row).get(0)));
            user.setChinese(Double.parseDouble(String.valueOf(excelVal.get(row).get(1))));
            user.setMath(Double.parseDouble(String.valueOf(excelVal.get(row).get(2))));
            user.setEnglish(Double.parseDouble(String.valueOf(excelVal.get(row).get(3))));
            //System.out.println("row-1="+(row-1)+"row="+row+"row+1="+(row+1));
            //System.out.println("user:"+user);
            users.add(user);
			//System.out.println("users:"+users);
        }
            UserService us=new UserServiceImpl();
            us.addBunchUsers(users);
    }

```

