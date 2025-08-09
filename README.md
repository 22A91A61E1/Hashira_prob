# Hashira_prob
import java.math.BigInteger;
import java.util.*;

public class Main {

    public static BigInteger decode(int base, String value) {
        return new BigInteger(value, base);
    }

    public static BigInteger getConstantTerm(int[] x, BigInteger[] y) {
        BigInteger result = BigInteger.ZERO;
        int n = x.length;
        for (int i = 0; i < n; i++) {
            BigInteger term = y[i];
            BigInteger numerator = BigInteger.ONE;
            BigInteger denominator = BigInteger.ONE;
            for (int j = 0; j < n; j++) {
                if (j != i) {
                    numerator = numerator.multiply(BigInteger.valueOf(-x[j]));
                    denominator = denominator.multiply(BigInteger.valueOf(x[i] - x[j]));
                }
            }
            term = term.multiply(numerator).divide(denominator);
            result = result.add(term);
        }
        return result;
    }

    public static void processJSON(String json) {
        json = json.trim().replaceAll("[\\n\\r]", "");

        // Extract n and k
        int n = Integer.parseInt(json.replaceAll(".\"n\"\\s:\\s*(\\d+).*", "$1"));
        int k = Integer.parseInt(json.replaceAll(".\"k\"\\s:\\s*(\\d+).*", "$1"));

        // Extract points
        Map<Integer, String[]> data = new TreeMap<>();
        String[] parts = json.split("\\},");
        for (String part : parts) {
            if (part.contains("\"base\"") && part.contains("\"value\"")) {
                String keyStr = part.replaceAll(".\"(\\d+)\"\\s:\\s*\\{.*", "$1");
                String base = part.replaceAll(".\"base\"\\s:\\s*\"(\\d+)\".*", "$1");
                String value = part.replaceAll(".\"value\"\\s:\\s*\"([0-9a-zA-Z]+)\".*", "$1");
                try {
                    int key = Integer.parseInt(keyStr);
                    data.put(key, new String[]{base, value});
                } catch (NumberFormatException e) {
                    // ignore non-numeric keys like "keys"
                }
            }
        }

        // Take first k points
        int count = 0;
        int[] xArr = new int[k];
        BigInteger[] yArr = new BigInteger[k];
        for (Map.Entry<Integer, String[]> e : data.entrySet()) {
            if (count == k) break;
            xArr[count] = e.getKey();
            yArr[count] = decode(Integer.parseInt(e.getValue()[0]), e.getValue()[1]);
            count++;
        }

        BigInteger constant = getConstantTerm(xArr, yArr);
        System.out.println(constant);
    }

    public static void main(String[] args) {
        String json1 = "{\n" +
                "\"keys\": {\"n\": 4, \"k\": 3},\n" +
                "\"1\": {\"base\": \"10\", \"value\": \"4\"},\n" +
                "\"2\": {\"base\": \"2\", \"value\": \"111\"},\n" +
                "\"3\": {\"base\": \"10\", \"value\": \"12\"},\n" +
                "\"6\": {\"base\": \"4\", \"value\": \"213\"}\n" +
                "}";

        String json2 = "{\n" +
                "\"keys\": {\"n\": 10, \"k\": 7},\n" +
                "\"1\": {\"base\": \"6\", \"value\": \"13444211440455345511\"},\n" +
                "\"2\": {\"base\": \"15\", \"value\": \"aed7015a346d63\"},\n" +
                "\"3\": {\"base\": \"15\", \"value\": \"6aeeb69631c227c\"},\n" +
                "\"4\": {\"base\": \"16\", \"value\": \"e1b5e05623d881f\"},\n" +
                "\"5\": {\"base\": \"8\", \"value\": \"316034514573652620673\"},\n" +
                "\"6\": {\"base\": \"3\", \"value\": \"2122212201122002221120200210011020220200\"},\n" +
                "\"7\": {\"base\": \"3\", \"value\": \"20120221122211000100210021102001201112121\"},\n" +
                "\"8\": {\"base\": \"6\", \"value\": \"20220554335330240002224253\"},\n" +
                "\"9\": {\"base\": \"12\", \"value\": \"45153788322a1255483\"},\n" +
                "\"10\": {\"base\": \"7\", \"value\": \"1101613130313526312514143\"}\n" +
                "}";

        System.out.println("=== Test Case 1 ===");
        processJSON(json1);

        System.out.println("=== Test Case 2 ===");
        processJSON(json2);
    }
}
        
