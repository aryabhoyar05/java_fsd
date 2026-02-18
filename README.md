import java.util.*;
import java.util.function.Function;
import java.util.stream.Collectors;

class Order {

    private int orderId;
    private String status;
    private double amount;
    private Double tip;          // can be null
    private String notes;        // can be null

    public Order(int orderId, String status, double amount, Double tip, String notes) {
        this.orderId = orderId;
        this.status = status;
        this.amount = amount;
        this.tip = tip;
        this.notes = notes;
    }

    public int getOrderId() {
        return orderId;
    }

    public String getStatus() {
        return status;
    }

    public double getAmount() {
        return amount;
    }

    public Double getTip() {
        return tip;
    }

    public String getNotes() {
        return notes;
    }

    public void setAmount(double amount) {
        this.amount = amount;
    }
}

public class SwiggyOrderProcessing {

    public static void main(String[] args) {

        List<Order> orders = Arrays.asList(
                new Order(1, "DELIVERED", 600, 50.0, "Leave at door"),
                new Order(2, "CANCELLED", 400, null, null),
                new Order(3, "DELIVERED", 300, null, "Call on arrival"),
                new Order(4, "PENDING", 800, 20.0, null),
                new Order(5, "DELIVERED", 1000, null, null)
        );

        // Loyalty Discount Function (10% discount above 500)
        Function<Double, Double> loyaltyDiscount =
                amount -> amount > 500 ? amount * 0.9 : amount;

        List<Order> finalOrders = orders.stream()

                // 1. Filter DELIVERED orders
                .filter(order -> order.getStatus().equals("DELIVERED"))

                // 2. Apply discount using functional parameter
                .map(order -> {
                    double discountedAmount = loyaltyDiscount.apply(order.getAmount());
                    order.setAmount(discountedAmount);
                    return order;
                })

                // 4. Sort by final amount
                .sorted(Comparator.comparing(Order::getAmount))

                .collect(Collectors.toList());

        // 3 & 4. Handle Optional values and print report
        System.out.println("Final Billing Report:");

        finalOrders.forEach(order -> {

            double tip = Optional.ofNullable(order.getTip()).orElse(0.0);
            String note = Optional.ofNullable(order.getNotes()).orElse("No Notes");

            System.out.println("Order ID: " + order.getOrderId()
                    + " | Amount: ₹" + order.getAmount()
                    + " | Tip: ₹" + tip
                    + " | Notes: " + note);
        });
    }
}
