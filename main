import javax.swing.*;
import java.awt.*;
import java.awt.event.MouseAdapter;
import java.awt.event.MouseEvent;
import java.awt.image.BufferedImage;

public class SwingPaint {

    private static Color currentColor = Color.BLACK;
    private static final Color BACKGROUND_COLOR = Color.WHITE;
    private static boolean isErasing = false;
    private static int currentThickness = 5; // Default pen thickness

    // New variables for shape drawing
    private static String drawingMode = "freehand"; // "freehand", "rectangle", "oval"
    private static boolean isFilling = false;
    private static JToggleButton fillButton;
    private static JComboBox<String> shapeSelector;

    public static void main(String[] args) {
        SwingUtilities.invokeLater(() -> {
            JFrame frame = new JFrame("Basic Java Paint with Controls");
            frame.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
            frame.setSize(800, 600);

            // Create the drawing area
            DrawArea drawArea = new DrawArea();

            // Create the control panel for buttons and slider
            JPanel controls = new JPanel();
            JButton colorButton = new JButton("Select Color");
            JToggleButton eraseButton = new JToggleButton("Eraser");

            // New JComboBox for shape selection
            String[] shapeOptions = {"Freehand", "Rectangle", "Oval"};
            shapeSelector = new JComboBox<>(shapeOptions);

            // Toggle button for filling shapes
            fillButton = new JToggleButton("Fill", false);

            // Create and configure the thickness slider
            JSlider thicknessSlider = new JSlider(JSlider.HORIZONTAL, 1, 20, currentThickness);
            thicknessSlider.setMajorTickSpacing(5);
            thicknessSlider.setMinorTickSpacing(1);
            thicknessSlider.setPaintTicks(true);
            thicknessSlider.setPaintLabels(true);

            // Action listener for the color button
            colorButton.addActionListener(e -> {
                Color newColor = JColorChooser.showDialog(
                        frame,
                        "Choose a drawing color",
                        currentColor
                );
                if (newColor != null) {
                    currentColor = newColor;
                    eraseButton.setSelected(false);
                    isErasing = false;
                    shapeSelector.setEnabled(true);
                    fillButton.setEnabled(true);
                }
            });

            // Action listener for the eraser toggle button
            eraseButton.addActionListener(e -> {
                isErasing = eraseButton.isSelected();
                if (isErasing) {
                    shapeSelector.setEnabled(false);
                    fillButton.setEnabled(false);
                    // Automatically switch to freehand mode for erasing
                    shapeSelector.setSelectedItem("Freehand");
                    drawingMode = "freehand";
                } else {
                    shapeSelector.setEnabled(true);
                    fillButton.setEnabled(true);
                }
            });

            // Item listener for the shape selector JComboBox
            shapeSelector.addItemListener(e -> {
                String selected = (String) shapeSelector.getSelectedItem();
                drawingMode = selected.toLowerCase();
            });
            
            // Action listener for fill button
            fillButton.addActionListener(e -> isFilling = fillButton.isSelected());

            // Change listener for the thickness slider
            thicknessSlider.addChangeListener(e -> {
                JSlider source = (JSlider) e.getSource();
                if (!source.getValueIsAdjusting()) {
                    currentThickness = source.getValue();
                }
            });

            controls.add(colorButton);
            controls.add(eraseButton);
            controls.add(new JSeparator(SwingConstants.VERTICAL));
            controls.add(new JLabel("Shape:"));
            controls.add(shapeSelector);
            controls.add(fillButton);
            controls.add(new JLabel("Thickness:"));
            controls.add(thicknessSlider);

            // Add the controls panel and drawing area to the frame
            frame.add(controls, BorderLayout.NORTH);
            frame.add(drawArea, BorderLayout.CENTER);

            frame.setVisible(true);
        });
    }

    private static class DrawArea extends JPanel {
        private BufferedImage canvas;
        private Point lastPoint;
        private Point startPoint;
        private Point endPoint;

        public DrawArea() {
            canvas = new BufferedImage(800, 600, BufferedImage.TYPE_INT_ARGB);
            Graphics2D g2d = canvas.createGraphics();
            g2d.setColor(BACKGROUND_COLOR);
            g2d.fillRect(0, 0, 800, 600);
            g2d.dispose();

            addMouseMotionListener(new MouseAdapter() {
                @Override
                public void mouseDragged(MouseEvent e) {
                    if (drawingMode.equals("freehand")) {
                        if (lastPoint != null) {
                            Graphics2D g2d = (Graphics2D) canvas.getGraphics();
                            g2d.setStroke(new BasicStroke(currentThickness, BasicStroke.CAP_ROUND, BasicStroke.JOIN_ROUND));
                            if (isErasing) {
                                g2d.setColor(BACKGROUND_COLOR);
                            } else {
                                g2d.setColor(currentColor);
                            }
                            g2d.drawLine(lastPoint.x, lastPoint.y, e.getX(), e.getY());
                            g2d.dispose();
                            repaint();
                        }
                        lastPoint = e.getPoint();
                    } else { // For shapes
                        endPoint = e.getPoint();
                        repaint(); // Repaint to show the shape preview
                    }
                }
            });

            addMouseListener(new MouseAdapter() {
                @Override
                public void mousePressed(MouseEvent e) {
                    if (drawingMode.equals("freehand")) {
                        lastPoint = e.getPoint();
                    } else { // For shapes
                        startPoint = e.getPoint();
                        endPoint = e.getPoint();
                    }
                }

                @Override
                public void mouseReleased(MouseEvent e) {
                    if (drawingMode.equals("freehand")) {
                        lastPoint = null;
                    } else { // For shapes
                        endPoint = e.getPoint();
                        drawShape(startPoint, endPoint);
                        startPoint = null;
                        endPoint = null;
                        repaint();
                    }
                }
            });
        }

        private void drawShape(Point p1, Point p2) {
            Graphics2D g2d = (Graphics2D) canvas.getGraphics();
            g2d.setStroke(new BasicStroke(currentThickness, BasicStroke.CAP_ROUND, BasicStroke.JOIN_ROUND));
            g2d.setColor(currentColor);

            int x = Math.min(p1.x, p2.x);
            int y = Math.min(p1.y, p2.y);
            int width = Math.abs(p1.x - p2.x);
            int height = Math.abs(p1.y - p2.y);

            if (drawingMode.equals("rectangle")) {
                if (isFilling) {
                    g2d.fillRect(x, y, width, height);
                } else {
                    g2d.drawRect(x, y, width, height);
                }
            } else if (drawingMode.equals("oval")) {
                if (isFilling) {
                    g2d.fillOval(x, y, width, height);
                } else {
                    g2d.drawOval(x, y, width, height);
                }
            }
            g2d.dispose();
        }

        @Override
        protected void paintComponent(Graphics g) {
            super.paintComponent(g);
            g.drawImage(canvas, 0, 0, null);

            // Draw shape preview while dragging
            if (!drawingMode.equals("freehand") && startPoint != null && endPoint != null) {
                Graphics2D g2d = (Graphics2D) g;
                g2d.setColor(currentColor);
                g2d.setStroke(new BasicStroke(currentThickness, BasicStroke.CAP_ROUND, BasicStroke.JOIN_ROUND));

                int x = Math.min(startPoint.x, endPoint.x);
                int y = Math.min(startPoint.y, endPoint.y);
                int width = Math.abs(startPoint.x - endPoint.x);
                int height = Math.abs(startPoint.y - endPoint.y);

                if (drawingMode.equals("rectangle")) {
                    if (isFilling) {
                        g2d.fillRect(x, y, width, height);
                    } else {
                        g2d.drawRect(x, y, width, height);
                    }
                } else if (drawingMode.equals("oval")) {
                    if (isFilling) {
                        g2d.fillOval(x, y, width, height);
                    } else {
                        g2d.drawOval(x, y, width, height);
                    }
                }
            }
        }
    }
}

