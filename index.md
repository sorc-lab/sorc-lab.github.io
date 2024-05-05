![Sorc Lab](/SorcLabLogo_White.png)

# Disk Utils
- [Cloning Disks](/blog/cloning-disks.md)
- [DD Copy](/blog/dd-copy.md)

<div class="status-box">
    <p>STATUS BOX</p>
</div>

<script>
    var statusElem = document.getElementById("statusBox");

    function calculateAverage(numbers) {
        if (numbers.length === 0) {
            return 0;
        }
        var sum = 0;
        for (var i = 0; i < numbers.length; i++) {
            sum += numbers[i];
        }
        var average = sum / numbers.length;
        return average;
    }

    function updateStatus() {
        var numbers = [58, 75, 45, 0];
        var average = calculateAverage(numbers);

        console.log("The average is: " + average);
        statusBox.textContent = average;
    }

    updateStatus();

    // setTimeout(function() {
    //     updateStatusBox("New content.");
    // }, 3000);
</script>

<!-- BK
![Sorc Lab](/SorcLabLogo_White.png)

# Disk Utils
- [Cloning Disks](/blog/cloning-disks.md)
- [DD Copy](/blog/dd-copy.md)

-->
