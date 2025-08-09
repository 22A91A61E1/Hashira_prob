# Hashira_prob
import com.google.code.gson.Gson;
import java.util.Map;

public class PolynomialSolver {
    // Evaluates polynomial at x given coefficients (index 0 is highest degree)
    private static double evaluatePolynomial(double[] coefficients, double x) {
        double result = 0;
        for (int i = 0; i < coefficients.length; i++) {
            result += coefficients[i] * Math.pow(x, coefficients.length - 1 - i);
        }
        return result;
    }

    // Bisection method to find root of polynomial - y = 0
    private static double findRoot(double[] coefficients, double y, double a, double b, double tolerance) {
        double fa = evaluatePolynomial(coefficients, a) - y;
        double fb = evaluatePolynomial(coefficients, b) - y;
        if (fa * fb >= 0) {
            throw new IllegalArgumentException("Function must have opposite signs at interval ends");
        }
        while (b - a > tolerance) {
            double mid = (a + b) / 2;
            double fm = evaluatePolynomial(coefficients, mid) - y;
            if (Math.abs(fm) < tolerance) return mid;
            if (fa * fm < 0) {
                b = mid;
                fb = fm;
            } else {
                a = mid;
                fa = fm;
            }
        }
        return (a + b) / 2;
    }

    public static void main(String[] args) {
        try {
            // Embedded JSON data
            String jsonString = "{"
                + "\"coefficients\": [1, 0, -2],"
                + "\"y_encoded\": {"
                + "\"base\": \"16\","
                + "\"value\": \"4\""
                + "}"
                + "}";

            // Parse JSON using Gson
            Gson gson = new Gson();
            Map<String, Object> json = gson.fromJson(jsonString, Map.class);

            // Extract coefficients
            double[] coefficients = ((java.util.List<?>) json.get("coefficients")).stream()
                .mapToDouble(obj -> ((Number) obj).doubleValue())
                .toArray();

            // Decode y-value
            @SuppressWarnings("unchecked")
            Map<String, String> yEncoded = (Map<String, String>) json.get("y_encoded");
            String base = yEncoded.get("base");
            String value = yEncoded.get("value");
            double y = Integer.parseInt(value, Integer.parseInt(base));

            // Find x using bisection method (assuming root exists in [-100, 100])
            double x = findRoot(coefficients, y, -100, 100, 1e-6);

            // Compute constant (polynomial evaluated at x)
            double constant = evaluatePolynomial(coefficients, x);

            // Print only the constant value
            System.out.printf("%.6f%n", constant);

        } catch (Exception e) {
            System.err.println("Error processing data: " + e.getMessage());
        }
    }
}
