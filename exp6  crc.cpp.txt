#include <stdio.h>
#include <string.h>

// Function to calculate CRC remainder
void crc(char data[], char generator[], char remainder[])
{
    // Find length of data and generator
    int dataLen = strlen(data);
    int genLen = strlen(generator);
    char temp[100];

    // Copy data into temporary array
    strcpy(temp, data);

    // Perform XOR division
    for (int i = 0; i <= dataLen - genLen; i++)
    {
        // Division is performed only when current bit is 1
        if (temp[i] == '1')
        {
            // XOR current bits with generator bits
            for (int j = 0; j < genLen; j++)
            {
                if (temp[i + j] == generator[j])
                    temp[i + j] = '0';   // Same bits -> 0
                else
                    temp[i + j] = '1';   // Different bits -> 1
            }
        }
    }

    // The last (genLen - 1) bits are the CRC remainder
    strcpy(remainder, &temp[dataLen - genLen + 1]);
}

int main()
{
    char data[100], generator[100];
    char appendedData[100], remainder[100];
    char received[100];

    // Take original data from user (up to 100 characters)
    printf("Enter data: ");
    scanf("%s", data);

    // Take generator from user
    printf("Enter generator: ");
    scanf("%s", generator);

    // Find lengths
    int dataLen = strlen(data);
    int genLen = strlen(generator);

    // Copy original data
    strcpy(appendedData, data);

    // Add (generator length - 1) zeros to the data
    // Example: Data = 110101, Generator length = 4 -> 110101000
    for (int i = 0; i < genLen - 1; i++)
        appendedData[dataLen + i] = '0';

    // Add NULL character at the end of string
    appendedData[dataLen + genLen - 1] = '\0';

    // Calculate and display CRC remainder
    crc(appendedData, generator, remainder);
    printf("\nCRC Remainder: %s", remainder);

    // Add CRC remainder to original data to form the transmitted codeword
    strcpy(received, data);
    strcat(received, remainder);

    // Display transmitted codeword
    printf("\nTransmitted Codeword: %s", received);

    // Receiver side
    printf("\n\nEnter received codeword: ");
    scanf("%s", received);

    // Perform CRC division on received codeword
    int error = 0;
    crc(received, generator, remainder);

    // Check whether CRC remainder contains any 1
    for (int i = 0; i < (int)strlen(remainder); i++)
    {
        if (remainder[i] != '0')
        {
            // If any bit is 1, error is detected
            error = 1;
            break;
        }
    }

    // Display result
    if (error)
        printf("\nError detected in received data.\n");
    else
        printf("\nNo error detected. Data received correctly.\n");

    return 0;
}