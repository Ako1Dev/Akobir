# Akobir
import com.google.gson.Gson;
import model.Todos;
import org.apache.http.HttpRequest;
import org.apache.http.HttpResponse;
import org.apache.http.client.HttpClient;
import org.apache.http.client.methods.HttpGet;
import org.apache.http.impl.client.HttpClients;
import org.apache.poi.hssf.usermodel.HSSFCellStyle;
import org.apache.poi.ss.usermodel.*;
import org.apache.poi.xssf.usermodel.XSSFWorkbook;

import javax.xml.ws.Response;
import java.io.*;

public class ApiTodos {
    public static void main(String[] args) throws IOException {

        //bitta ish kitobi yani excel file yasadik
        Workbook workbook = new XSSFWorkbook();
        Sheet sheet = workbook.createSheet("Todos");
        //EXCEL HARIFLARI UCHUN FON VA KATALIK BERDIK
        Font fontHeader = workbook.createFont();
        fontHeader.setBold(true);
        fontHeader.setFontName("Times New Roman");
        fontHeader.setFontHeightInPoints((short) 20);

        //YACHEYKALARGA STYLE BERDIK(RANG, gRANITSA, YOZUV JOYLASHUV)
        CellStyle cellStyleHeader = workbook.createCellStyle();
        cellStyleHeader.setAlignment(HorizontalAlignment.CENTER);
        cellStyleHeader.setFillForegroundColor(IndexedColors.GREEN.getIndex());
        cellStyleHeader.setFillPattern(FillPatternType.SOLID_FOREGROUND);
        cellStyleHeader.setBorderBottom(HSSFCellStyle.BORDER_THIN);
        cellStyleHeader.setBorderTop(HSSFCellStyle.BORDER_THIN);
        cellStyleHeader.setBorderLeft(HSSFCellStyle.BORDER_THIN);
        cellStyleHeader.setBorderRight(HSSFCellStyle.BORDER_THIN);
        cellStyleHeader.setFont(fontHeader);

        Font fontBody = workbook.createFont();
        fontBody.setBold(true);
        fontBody.setFontName("Classic");
        fontBody.setFontHeightInPoints((short) 14);


        CellStyle cellStyleBody = workbook.createCellStyle();
        cellStyleBody.setAlignment(HorizontalAlignment.CENTER);
        cellStyleBody.setFillForegroundColor(IndexedColors.YELLOW.getIndex());
        cellStyleBody.setFillPattern(FillPatternType.SOLID_FOREGROUND);
        cellStyleBody.setBorderBottom(HSSFCellStyle.BORDER_THIN);
        cellStyleBody.setBorderTop(HSSFCellStyle.BORDER_THIN);
        cellStyleBody.setBorderLeft(HSSFCellStyle.BORDER_THIN);
        cellStyleBody.setBorderRight(HSSFCellStyle.BORDER_THIN);
        cellStyleBody.setFont(fontBody);


        //excelga qator yasadik
        Row row = sheet.createRow(0);
        //yasagan qatorga katak yasab oldik
        Cell cell = row.createCell(0);
        //YASAGAN YACHEYKAGA STYLE BERDIK
        cell.setCellStyle(cellStyleHeader);
        //katagimizga malumot yoszdik
        cell.setCellValue("UserId");
        cell.setCellStyle(cellStyleHeader);
        cell = row.createCell(1);
        cell.setCellValue("Id");
        cell.setCellStyle(cellStyleHeader);
        cell = row.createCell(2);
        cell.setCellValue("title");
        cell.setCellStyle(cellStyleHeader);
        cell = row.createCell(3);
        cell.setCellValue("completed");
        cell.setCellStyle(cellStyleHeader);

        HttpClient httpClient = HttpClients.createDefault();
        HttpGet httpGet = new HttpGet("https://jsonplaceholder.typicode.com/todos");
        HttpResponse response = httpClient.execute(httpGet);
        BufferedReader bufferedReader = new BufferedReader(new InputStreamReader(response.getEntity().getContent()));
        String s = bufferedReader.readLine();
        StringBuilder stringBuilder = new StringBuilder();

        while (s != null) {
            stringBuilder.append(s);
            s = bufferedReader.readLine();
        }

        BufferedWriter bufferedWriter = new BufferedWriter(new FileWriter("todos.txt"));
        bufferedWriter.write(stringBuilder.toString());
        bufferedWriter.close();
        System.out.println("Yozildi");

        BufferedReader bufferedReader1 = new BufferedReader(new FileReader("todos.txt"));
        String s1 = bufferedReader1.readLine();
        Gson gson = new Gson();
        Todos[] todos = gson.fromJson(s1, Todos[].class);
        int a = 0;
        for (Todos todo : todos) {
            a++;
            row = sheet.createRow(a);
            cell = row.createCell(0);
            cell.setCellValue(todo.getUserId());
            cell.setCellStyle(cellStyleBody);
            cell = row.createCell(1);
            cell.setCellValue(todo.getId());
            cell.setCellStyle(cellStyleBody);
            cell = row.createCell(2);
            cell.setCellValue(todo.getTitle());
            cell.setCellStyle(cellStyleBody);
            cell = row.createCell(3);
            cell.setCellValue(todo.isCompleted() ? "Bajarilgan" : "Bajarilmagan");
            cell.setCellStyle(cellStyleBody);
        }
        OutputStream outputStream = new FileOutputStream(new File("todos.xlsx"));
        workbook.write(outputStream);
        workbook.close();
    }
}


